# Build Setup Fixes

## CMake Version Compatibility Error

### Error Message
```
CMake Error at src/drivers/ins/vectornav/libvnc/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 has been removed from CMake.
```

### Cause
The VectorNav library's CMakeLists.txt file specified an outdated minimum CMake version (2.8.4), which is no longer supported by modern CMake versions.

### Fix
Updated `/Users/dan/Documents/testing/PX4-Autopilot/src/drivers/ins/vectornav/libvnc/CMakeLists.txt`:
- Changed `cmake_minimum_required(VERSION 2.8.4)` to `cmake_minimum_required(VERSION 3.5)`

This is compatible with the main PX4 project which requires CMake 3.9.

---

## Uninitialized Variable Compilation Error

### Error Message
```
/Users/dan/Documents/testing/PX4-Autopilot/platforms/nuttx/NuttX/nuttx/include/arch/armv7-m/irq.h:362:3: error: 'primask' is used uninitialized [-Werror=uninitialized]
```

### Cause
The `primask` variable in the `raisebasepri()` function was declared but not initialized. While the inline assembly immediately reads from the PRIMASK register (overwriting the value), the compiler's static analysis flags this as an uninitialized variable usage.

### Fix
Updated `/Users/dan/Documents/testing/PX4-Autopilot/platforms/nuttx/NuttX/nuttx/include/arch/armv7-m/irq.h`:
- Changed `register uint32_t primask;` to `register uint32_t primask = 0;`

This initialization is safe because the inline assembly's `mrs` instruction immediately overwrites the value with the actual PRIMASK register contents.

---

## Notes
- These fixes are specific to the PX4-Autopilot build system with NuttX
- The CMake fix ensures compatibility with modern CMake versions
- The uninitialized variable fix silences a false positive warning while maintaining correct behavior

