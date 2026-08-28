# Testing Anti-Patterns

**Load this reference when:** writing or changing tests, adding mocks, or tempted to add test-only methods to production code.

## Overview

Tests must verify real behavior, not mock behavior. Mocks are a means to isolate, not the thing being tested.

**Core principle:** Test what the code does, not what the mocks do.

**Following strict TDD prevents these anti-patterns.**

## The Iron Laws

```
1. NEVER test mock behavior
2. NEVER add test-only methods to production classes
3. NEVER mock without understanding dependencies
```

## Anti-Pattern 1: Testing Mock Behavior

**The violation:**
```python
class TestPriceCalculator:
    def test_calculates_price_with_tax(self) -> None:
        """Test that the price calculator calculates the price with tax.

        Given a base price and country.
        When calculating the final price.
        Then the tax service is used.
        """
        # Arrange
        mock_tax_service = MagicMock()
        calculator = PriceCalculator(tax_service=mock_tax_service)

        # Act
        calculator.calculate(base_price=100.00, country="US")

        # Assert
        assert mock_tax_service.called  # ❌ Verifying mock existence, not calculation result
```

**Why this is wrong:**
- You're verifying the mock was touched, not that the result is correct
- Test passes when mock is present, fails when it's not
- Tells you nothing about whether the calculation is right

**your human partner's correction:** "Are we testing the behavior of a mock?"

**The fix:**
```python
class TestPriceCalculator:
    def test_calculates_price_with_tax(self) -> None:
        """Test that the price calculator calculates the price with tax.
        
        Given a base price and a tax service returning 10%.
        When calculating the final price for country 'US'.
        Then the result includes the tax applied to the base price.
        """
        # Arrange
        fake_tax_service = FakeTaxService(rate=0.10)
        calculator = PriceCalculator(tax_service=fake_tax_service)

        # Act
        result = calculator.calculate(base_price=100.00, country="US")

        # Assert
        assert result == pytest.approx(110.00), (
            "Price should be base price plus 10% tax"
        )
```

Use a fake with real behavior instead of a mock. Assert on the output, not on the mock.

### Gate Function

```
BEFORE asserting on any mock or mock method:
  Ask: "Am I testing the result of the code or just that the mock was invoked?"

  IF testing mock invocation only:
    STOP - Replace the mock with a fake or assert on real output instead

  Test real output instead
```

## Anti-Pattern 2: Test-Only Methods in Production

**The violation:**
```python
# ❌ BAD: reset() only called in test fixtures
class DataPipeline:
    def reset(self) -> None:
        self._processed_ids.clear()
        self._error_log = []
```

```python
# In conftest.py
@pytest.fixture(autouse=True)
def clean_pipeline(pipeline: DataPipeline) -> Generator[None, None, None]:
    yield
    pipeline.reset()  # ❌ Calling test-only production method
```

**Why this is wrong:**
- Production class polluted with test-only code
- Dangerous if accidentally called in production
- Violates YAGNI and separation of concerns
- Confuses object lifecycle with test lifecycle

**The fix:**
```python
# ✅ GOOD: DataPipeline has no reset() — test utility handles cleanup

# In conftest.py or test_helpers.py
def reset_pipeline(pipeline: DataPipeline) -> None:
    """Test utility: clear pipeline state between tests."""
    pipeline._processed_ids.clear()
    pipeline._error_log = []

@pytest.fixture(autouse=True)
def clean_pipeline(pipeline: DataPipeline) -> Generator[None, None, None]:
    yield
    reset_pipeline(pipeline)  # ✅ Test utility, not a production method
```

### Gate Function

```
BEFORE adding any method to a production class:
  Ask: "Is this only called from test files or fixtures?"

  IF yes:
    STOP - Don't add it
    Put it in conftest.py or a test utility module instead

  Ask: "Does this class own this resource's lifecycle?"

  IF no:
    STOP - Wrong class for this method
```

## Anti-Pattern 3: Mocking Without Understanding

**The violation:**
```python
# ❌ BAD: Mock removes the side effect the duplicate check depends on
class TestOrderProcessor:
    def test_rejects_duplicate_order(self, mocker: MockerFixture) -> None:
        """
        Given an order already submitted.
        When submitting the same order ID again.
        Then a DuplicateOrderError is raised.
        """
        # This also suppresses order ID registration — the very thing the duplicate check reads!
        mocker.patch("order_processor.notify_warehouse")

        processor = OrderProcessor()
        processor.submit(order_id="ord-1", items=[{"sku": "A", "qty": 1}])
        processor.submit(order_id="ord-1", items=[{"sku": "A", "qty": 1}])  # Should raise — but won't
```

**Why this is wrong:**
- `notify_warehouse` also registers the order ID as a side effect
- Mocking it to "avoid the slow HTTP call" removes the state the duplicate check reads
- Test passes for the wrong reason or silently never detects the duplicate

**The fix:**
```python
# ✅ GOOD: Mock only the slow external call, preserve the registration side effect
class TestOrderProcessor:
    def test_rejects_duplicate_order(self, mocker: MockerFixture) -> None:
        """
        Given an order already submitted.
        When submitting the same order ID again.
        Then a DuplicateOrderError is raised.
        """
        mocker.patch("order_processor.WarehouseClient.post")  # Mock only the HTTP call

        processor = OrderProcessor()
        processor.submit(order_id="ord-1", items=[{"sku": "A", "qty": 1}])  # ID registered ✓

        with pytest.raises(DuplicateOrderError):
            processor.submit(order_id="ord-1", items=[{"sku": "A", "qty": 1}])  # Duplicate detected ✓
```

### Gate Function

```
BEFORE mocking any method:
  STOP - Don't mock yet

  1. Ask: "What side effects does the real method have?"
  2. Ask: "Does this test depend on any of those side effects?"
  3. Ask: "Do I fully understand what this test needs?"

  IF depends on side effects:
    Mock at lower level (the actual slow/external operation)
    OR use test doubles that preserve necessary behavior
    NOT the high-level method the test depends on

  IF unsure what test depends on:
    Run test with real implementation FIRST
    Observe what actually needs to happen
    THEN add minimal mocking at the right level

  Red flags:
    - "I'll mock this to be safe"
    - "This might be slow, better mock it"
    - Mocking without understanding the dependency chain
```

## Anti-Pattern 4: Incomplete Mocks

**The violation:**
```python
# ❌ BAD: Partial dict — only fields you think you need right now
mock_response: dict[str, object] = {
    "status": "success",
    "data": {"user_id": "123", "name": "Alice"},
    # Missing: "metadata" that downstream processing accesses
}

# Later: KeyError when code does response["metadata"]["request_id"]
```

**Why this is wrong:**
- **Partial mocks hide structural assumptions** — you only mocked fields you know about
- **Downstream code may depend on fields you omitted** — silent `KeyError` or `AttributeError`
- **Tests pass but integration fails** — mock is incomplete, real API is not
- **False confidence** — test proves nothing about real behavior

**The Iron Rule:** Mock the COMPLETE data structure as it exists in reality, not just fields your immediate test uses.

**The fix:**
```python
# ✅ GOOD: Mirror the real API response in full
mock_response: ApiResponse = {
    "status": "success",
    "data": {"user_id": "123", "name": "Alice"},
    "metadata": {"request_id": "req-789", "timestamp": 1234567890},
}
```

Use a `TypedDict` to make the complete structure explicit and catch omissions statically:

```python
class ApiResponse(TypedDict):
    status: str
    data: dict[str, str]
    metadata: dict[str, int | str]
```

### Gate Function

```
BEFORE creating mock responses:
  Check: "What fields does the real API response contain?"

  Actions:
    1. Examine actual API response from docs/examples
    2. Include ALL fields the system might consume downstream
    3. Verify mock matches real response schema completely

  Critical:
    If you're creating a mock, you must understand the ENTIRE structure.
    Partial mocks fail silently when code depends on omitted fields.

  If uncertain: include all documented fields
```

## Anti-Pattern 5: Tests as Afterthought

**The violation:**
```
✅ Implementation complete
❌ No tests written
"Ready for testing"
```

**Why this is wrong:**
- Testing is part of implementation, not an optional follow-up
- TDD would have caught this
- Can't claim complete without tests

**The fix:**
```
TDD cycle:
1. Write failing test
2. Implement to pass
3. Refactor
4. THEN claim complete
```

## When Mocks Become Too Complex

**Warning signs:**
- Mock setup longer than test logic
- Mocking everything to make test pass
- `MagicMock()` used without `spec=` when the real class is available
- Test breaks when mock changes

**your human partner's question:** "Do we need to be using a mock here?"

**Consider:** A fake (in-memory implementation) is often simpler and safer than a heavily-configured `MagicMock`.

## TDD Prevents These Anti-Patterns

**Why TDD helps:**
1. **Write test first** → Forces you to think about what you're actually testing
2. **Watch it fail** → Confirms the test tests real behavior, not mocks
3. **Minimal implementation** → No test-only methods creep into production classes
4. **Real dependencies** → You see what the test actually needs before mocking

**If you're testing mock behavior, you violated TDD** — you added mocks without watching the test fail against real code first.

## Quick Reference

| Anti-Pattern | Fix |
|--------------|-----|
| `assert mock_x.called` instead of asserting output | Use a fake or assert on real result |
| Test-only methods in production classes | Move to `conftest.py` or test utility module |
| Mock without understanding side effects | Understand dependencies first, mock minimally |
| Incomplete mock dicts or objects | Mirror real structure completely; use `TypedDict` |
| Tests as afterthought | TDD — tests first |
| Over-complex `MagicMock` setup | Consider a fake (in-memory implementation) |

## Red Flags

- `assert mock_x.called` with no check on arguments or state
- `MagicMock()` without `spec=` when the real class is available
- Methods in production classes only called inside `@pytest.fixture` teardown
- `mocker.patch` path targets the definition module, not where the name is imported
- Mock response dict missing keys that the real API returns
- Mock setup is >50% of the test body
- Test fails when you remove the mock
- Can't explain why the mock is needed
- Mocking "just to be safe"

## The Bottom Line

**Mocks are tools to isolate, not things to test.**

If TDD reveals you're testing mock behavior, you've gone wrong.

Fix: Test real behavior or question why you're mocking at all.
