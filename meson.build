project(
  'vnpy_sec',
  'cpp',
  version: '1.0.5',
  license: 'MIT',
  meson_version: '>=1.7.0',
  default_options: [
    'buildtype=release',
    'cpp_std=c++17',
    'warning_level=2',
  ],
)

# 导入文件系统和Python模块
fs = import('fs')
py = import('python').find_installation(pure: false)
py_dep = py.dependency()

# 输出构建目标系统信息
message('构建目标系统: ' + host_machine.system())

# 只在 Windows 平台编译 C++ 扩展模块
if host_machine.system() == 'windows'
  message('检测到 Windows 平台，将编译 C++ 扩展模块')
  
  # 获取pybind11路径
  pybind11_include_dir = run_command('python', '-c', 'import pybind11; print(pybind11.get_include())', check: true).stdout().strip()
  message('使用pybind11路径: ' + pybind11_include_dir)
  
  # 获取编译器信息
  cpp = meson.get_compiler('cpp')
  
  # Windows编译器设置
  add_project_arguments('/MT', language : 'cpp')
  
  # 设置库目录
  lib_dir = meson.current_source_dir() / 'vnpy_sec/api/libs'
  api_dir = meson.current_source_dir() / 'vnpy_sec/api'
  
  # 设置include目录
  include_dirs = include_directories(
    'vnpy_sec/api/include',
    'vnpy_sec/api/vnsec',
    pybind11_include_dir,
  )
  
  # 定义SEC库
  DFITCSECMdApi_lib = cpp.find_library('DFITCSECMdApi', 
                                       dirs: [lib_dir, api_dir],
                                       required: true)
  DFITCSECTraderApi_lib = cpp.find_library('DFITCSECTraderApi', 
                                       dirs: [lib_dir, api_dir],
                                       required: true)
  HsFutuSystemInfo_lib = cpp.find_library('HsFutuSystemInfo', 
                                       dirs: [lib_dir, api_dir],
                                       required: true)
  HsFutuSystemInfo_lib = cpp.find_library('HsFutuSystemInfo', 
                                       dirs: [lib_dir, api_dir],
                                       required: true)
  
  # 创建MD模块扩展
  md_module = py.extension_module(
    'vnsecmd',
    sources: ['vnpy_sec/api/vnsec/vnsecmd/vnsecmd.cpp'],
    include_directories: include_dirs,
    dependencies: [py_dep, DFITCSECMdApi_lib, DFITCSECTraderApi_lib, HsFutuSystemInfo_lib, HsFutuSystemInfo_lib],
    install: true,
    subdir: 'vnpy_sec/api'
  )

  # 创建TD模块扩展
  td_module = py.extension_module(
    'vnsectd',
    sources: ['vnpy_sec/api/vnsec/vnsectd/vnsectd.cpp'],
    include_directories: include_dirs,
    dependencies: [py_dep, DFITCSECMdApi_lib, DFITCSECTraderApi_lib, HsFutuSystemInfo_lib, HsFutuSystemInfo_lib],
    install: true,
    subdir: 'vnpy_sec/api'
  )
  
  # 安装API动态库文件
  api_libs = [
    'vnpy_sec/api/DFITCSECMdApi.dll',
    'vnpy_sec/api/DFITCSECTraderApi.dll',
    'vnpy_sec/api/HsFutuSystemInfo.dll',
    'vnpy_sec/api/HsFutuSystemInfo.dll',
  ]
  
  install_data(
    api_libs,
    install_dir: py.get_install_dir() / 'vnpy_sec/api'
  )
  
else
  # 非 Windows 平台提示
  warning('当前平台 (' + host_machine.system() + ') 不支持编译 C++ 扩展，仅安装 Python 源代码')
endif

# 安装Python源代码（所有平台都执行）
python_files = [
  ['vnpy_sec/__init__.py', 'vnpy_sec'],
  ['vnpy_sec/api/__init__.py', 'vnpy_sec/api'],
  ['vnpy_sec/api/sec_constant.py', 'vnpy_sec/api'],
  ['vnpy_sec/gateway/__init__.py', 'vnpy_sec/gateway'],
  ['vnpy_sec/gateway/sec_gateway.py', 'vnpy_sec/gateway'],
]

foreach file : python_files
  py.install_sources(
    [file[0]],
    pure: false,
    subdir: file[1]
  )
endforeach
