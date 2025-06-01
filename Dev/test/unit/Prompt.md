# 🧪 Unit Testing with Prophecy – Best Practices
## 🔧 Mocking Setup

- Use `ProphecyTrait` in all test classes  
- Declare prophecy properties using `ObjectProphecy|RealType`  
- Place `ObjectProphecy` first in the union type  
- Append `Prophecy` to the property name  
- Initialize prophecy objects in the `setUp()` method  
- Reveal prophecy objects when injecting into the system under test  
- Use Prophecy's API (`willReturn()`, `shouldBeCalledOnce()`, etc.) for expectations  

---

## 🧱 Test Structure

- Follow the Arrange-Act-Assert (AAA) pattern in every test  
- Arrange: Prepare test data and prophecy expectations  
- Act: Call the method under test  
- Assert: Verify results or exceptions  

---

## ✅ General Guidelines

- Use descriptive method names that explain the scenario and expected outcome  
- Keep tests focused on a single behavior  
- Ensure each test is independent (no shared state or dependencies)  
- Use test doubles (mocks, stubs, spies) to isolate the unit under test  
- Make assertions specific and meaningful  
- Separate success and failure scenarios into distinct test methods  
- Use data providers to test multiple input variations  
- Mock all external dependencies  
- Cover edge cases and boundary conditions
