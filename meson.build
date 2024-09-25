project('librepilot', 'cpp',
    default_options : ['warning_level=3',
                       'cpp_std=c++17',
                       'buildtype=debugoptimized'])

fs = import('fs')

toolchains = subproject('toolchains')
arm_enabled = toolchains.get_variable('arm_enabled')
assert(arm_enabled, 'The GPSLogger can only be build with an ARM toolchain.')

clang = toolchains.get_variable('clang')
llvm_link_cmd = toolchains.get_variable('llvm_link_cmd')
clang_base = toolchains.get_variable('clang_base')
c_std = toolchains.get_variable('c_std')
ir_flags = toolchains.get_variable('ir_flags')

libplatforms = subproject('ara-libplatforms')
clang_flags = libplatforms.get_variable('clang_flags')
arm_clang_flags = libplatforms.get_variable('arm_clang_flags')
arm_ld_flags = libplatforms.get_variable('arm_ld_flags')

arm_include_path = toolchains.get_variable('arm_include_path')

freertos_proj = subproject('freertos')
freertos_includes = []
foreach dir : freertos_proj.get_variable('header_dirs') + freertos_proj.get_variable('arm_header_dirs')
  freertos_includes += ['-I', dir]
endforeach

libsra_proj = subproject('ara-libsra')
libsra_includes = []
foreach dir : libsra_proj.get_variable('libsra_include_directories')
  libsra_includes += ['-I', dir]
endforeach

librepilot_cflags = [
  '-mthumb',
  '-mcpu=cortex-m3',
  '--specs=nano.specs',
  '-Os',
  '-gdwarf-2',
  # '-mapcs-frame',
  '-fomit-frame-pointer',
  '-Wall',
  '-Wextra',
  '-Wfloat-equal',
  '-Wdouble-promotion',
  '-fdata-sections',
  '-Wshadow',
  '-ffunction-sections',
  # '-std=gnu99',
  '-Wunsuffixed-float-constants',
  '-DUSE_INPUT_LPF',
  '-DUSE_GIMBAL_LPF',
  '-DUSE_GIMBAL_FF',
  '-DSTM32F10X',
  '-DSTM32F10X_MD',
  '-DSTM32F1',
  '-DUSE_STDPERIPH_DRIVER',
  '-DARM_MATH_CM3',
  '-DHSE_VALUE=8000000',
  '-DMODULE_ATTITUDE_BUILTIN',
  '-DMODULE_STABILIZATION_BUILTIN',
  '-DMODULE_ACTUATOR_BUILTIN',
  '-DMODULE_RECEIVER_BUILTIN',
  '-DMODULE_MANUALCONTROL_BUILTIN',
  '-DMODULE_FIRMWAREIAP_BUILTIN',
  '-DMODULE_TELEMETRY_BUILTIN',
  '-DHAS_CAMERASTAB_MODULE',
  '-DHAS_COMUSBBRIDGE_MODULE',
  '-DHAS_GPS_MODULE',
  '-DHAS_TXPID_MODULE',
  '-DHAS_OSDOUTPUT_MODULE',
  '-DHAS_UAVOMSPBRIDGE_MODULE',
  '-DHAS_UAVOMAVLINKBRIDGE_MODULE',
  '-DHAS_ATTITUDE_MODULE',
  '-DHAS_STABILIZATION_MODULE',
  '-DHAS_ACTUATOR_MODULE',
  '-DHAS_RECEIVER_MODULE',
  '-DHAS_MANUALCONTROL_MODULE',
  '-DHAS_FIRMWAREIAP_MODULE',
  '-DHAS_TELEMETRY_MODULE',
  '-DUSE_STM32103CB_CC_Rev1',
  '-DBOARD_TYPE=0x04',
  '-DBOARD_REVISION=0x02',
  '-DHW_TYPE=0x01',
  '-DBOOTLOADER_VERSION=0x04',
  '-DFW_BANK_BASE=0x08003000',
  '-DFW_BANK_SIZE=0x0001D000',
  '-DFW_DESC_SIZE=0x00000064',
  '-DBL_BANK_BASE=0x08000000',
  '-DBL_BANK_SIZE=0x00003000',
  '-DBL_DESC_SIZE=',
  '-mllvm', '-inline-threshold=2',
  # '-DARA_MOCK_UNROLL_MODINIT=1', # uncomment to 'unroll' the MODULE_INITIALISE_ALL loops in src code
  '-DconfigINCLUDE_ALL_DECLS=1',
]

here = meson.current_source_dir()
librepilot_includes = [
  '-I', here / 'flight/pios/common/libraries/FreeRTOS//Source/include',
  '-I', here / 'flight/pios/stm32f10x/inc',
  '-I', here / 'flight/pios/common/libraries/CMSIS/Include',
  '-I', here / 'flight/pios/stm32f10x/libraries/CMSIS/Core/CM3',
  '-I', here / 'flight/pios/stm32f10x/libraries/STM32F10x_StdPeriph_Driver/inc',
  '-I', here / 'flight/pios/stm32f10x/libraries/STM32_USB-FS-Device_Driver/inc',
  '-I', here / 'flight/pios/common/libraries/FreeRTOS//Source/portable/GCC/ARM_CM3',
  '-I', here / 'flight/pios',
  '-I', here / 'flight/pios/inc',
  '-I', here / 'flight/targets/boards/coptercontrol/firmware/..',
  '-I', here / 'flight/targets/boards/coptercontrol',
  '-I', here / 'flight/libraries/inc',
  '-I', here / 'flight/pios/common',
  '-I', here / 'flight/targets/boards/coptercontrol/firmware',
  '-I', here / 'flight/targets/boards/coptercontrol/firmware/inc',
  '-I', here / 'flight/libraries/math',
  '-I', here / 'flight/libraries/pid',
  '-I', here / 'flight/uavobjects/inc',
  '-I', here / 'flight/uavtalk/inc',
  '-I', here / 'firmware/uavobjects',
  '-I', here / 'flight/libraries/mavlink/v1.0/common',
  '-I', here / 'flight/modules/CameraStab/inc',
  '-I', here / 'flight/modules/ComUsbBridge/inc',
  '-I', here / 'flight/modules/GPS/inc',
  '-I', here / 'flight/modules/TxPID/inc',
  '-I', here / 'flight/modules/Osd/osdoutput/inc',
  '-I', here / 'flight/modules/UAVOMSPBridge/inc',
  '-I', here / 'flight/modules/UAVOMavlinkBridge/inc',
  '-I', here / 'flight/modules/Attitude/inc',
  '-I', here / 'flight/modules/Stabilization/inc',
  '-I', here / 'flight/modules/Actuator/inc',
  '-I', here / 'flight/modules/Receiver/inc',
  '-I', here / 'flight/modules/ManualControl/inc',
  '-I', here / 'flight/modules/FirmwareIAP/inc',
  '-I', here / 'flight/modules/Telemetry/inc',
  '-I', here / 'flight/modules/System/inc',
  '-I', arm_include_path,
] + freertos_includes + libsra_includes

librepilot_ldflags = [
  '--specs=nano.specs',
  '-T' + here / 'flight/pios/stm32f10x//link_STM32103CB_CC_Rev1_memory.ld',
  '-T' + here /'flight/pios/stm32f10x//link_STM32103CB_CC_Rev1_sections.ld',
  # '-Wl,--fatal-warnings',
  '-Wl,--sort-common',
  '-Wl,--sort-section=alignment',
  '-lm',
  # '-lc_nano',
  '-lgcc',
  '-Wl,-static',
  '-nostartfiles',
  '-Os',
]

librepilot_compile = generator(clang,
                               output: '@BASENAME@.ll',
                               depfile: '@BASENAME@_dep',
                               arguments: clang_base +
                                          [c_std] +
                                          librepilot_includes +
                                          ir_flags +
                                          librepilot_cflags +
                                          clang_flags +
                                          arm_clang_flags +
                                          arm_ld_flags
                              )

librepilot_mock_compile = generator(clang,
                                    output: '@BASENAME@.ll',
                                    depfile: '@BASENAME@_dep',
                                    arguments: clang_base +
                                               ['-DARA_MOCK'] +
                                               [c_std] +
                                               librepilot_includes +
                                               ir_flags +
                                               librepilot_cflags +
                                               clang_flags +
                                               arm_clang_flags +
                                               arm_ld_flags
                                   )

librepilot_targets = []
librepilot_mock_targets = []
subdir('firmware')
subdir('flight')

librepilot_application = custom_target('librepilot_app.ll',
                                       input: librepilot_targets,
                                       output: 'librepilot_app.ll',
                                       command: llvm_link_cmd)

librepilot_mock_application = custom_target('librepilot_mock_app.ll',
                                            input: librepilot_mock_targets,
                                            output: 'librepilot_mock.ll',
                                            command: llvm_link_cmd)


translation_map = files('function_pointer_translation_map.json')[0]

instance_corrections = files('instance_corrections.json')

freertos_config = fs.copyfile(freertos_proj.get_variable('default_config'), 'FreeRTOSConfig.h')

cloc_app = [here / 'flight', here / 'firmware']
