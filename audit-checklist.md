# Audit Check List

### High level:

- Read whitepaper and ensure it make sense
- Read docs and ensure it matches to whitepaper
- Read the code and ensure it matches to docs
- Read the tests and ensure it make sense and have sufficent coverage for edge cases

### Areas to check

- Docs
  - Docs for storages / events / calls / errors
  - Ensure all invariants/assumptions are explicitly documented
    - Better to be documented as an assertion / expect in code if possible
- Maintainability
  - Code is reasonably understandbale
  - Good variable/method/data structure names
- Arithmetic
  - Overflow (standard ops)
  - Lossy conversion (saturated ops)
  - Failable ops (checked ops, try into)
- Errors
  - No error return after storage mutation
  - `.expect` are indeed infallible
  - Handle errors gracefully if possible
    - But no errors shall be ignored
- Resources
  - Storage
    - Always a cost to fill storage / Cannot fill stroage for free
    - Incentive to free up storage / Offchain worker to cleanup storage
    - Always a hard limit for variable size item in storage
      - e.g. Number of elements in `Vec`
  - Computation
    - Calls have reasonable weight
    - If the weight depends on input, ensure there is a reasonable maximum weight
    - OnInitialize / OnFinalize have reasonable weight
    - Computational complexity should not be worser than O(n log(n)) if possible
      - Otherwise there should be a upper limit cap on n
      - Check every loop / for_each / recursion calls
- Crypto
  - Check storage key hashers are suitable
- Custom RPC
  - Reasonable computational complexity
  - Reasonable resource usage (memory & computation)
  - Ones require heavy resource usages should be marked as unsafe
- Unsigned Transaction
  - Ensure they cannot be abused to spam the network
    - i.e. validated before enter tx queue
- Integration
  - Check all the parameter values are sane
  - Ensure there are tests to validate the dependent parameters
    - e.g. Check the value of Dispatchable weights for FRAME pallets