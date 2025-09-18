project('it', 'cpp',
  default_options: ['buildtype=release', 'warning_level=2', 'b_lto=true', 'b_ndebug=if-release', 'cpp_std=c++11'],
  license: 'GPL-2.0',
  meson_version: '>=0.51.0',
  version: '1.2'
)

cxx = meson.get_compiler('cpp')

gcc_syntax = cxx.get_argument_syntax() == 'gcc'

opt_cflags = []
opt_defines = []

sources = [
  'src/vs_it_c.cpp',
  'src/vs_it.cpp',
  'src/vs_it_interface.cpp',
  'src/vs_it_process.cpp',
]

if gcc_syntax
  cpu_family = host_machine.cpu_family()
  if cpu_family == 'x86' or cpu_family == 'x86_64'
    isa_candidates = ['-mssse3', '-mmmx']
    detected_isa = ''
    foreach isa : isa_candidates
      if cxx.has_argument(isa)
        detected_isa = isa
        break
      endif
    endforeach

    if detected_isa != ''
      opt_cflags += detected_isa
      if detected_isa.startswith('-mssse')
        opt_defines += '-D__SSE'
        sources += 'src/vs_it_sse.cpp'
      else
        sources += 'src/vs_it_mmx.cpp'
      endif
    else
      opt_defines += '-D__C'
    endif
  else
    opt_defines += '-D__C'
  endif
endif

if cxx.has_argument('-ffast-math')
  opt_cflags += '-ffast-math'
endif

if gcc_syntax
  vapoursynth_dep = dependency('vapoursynth', version: '>=55').partial_dependency(compile_args: true, includes: true)
  install_dir = vapoursynth_dep.get_variable(pkgconfig: 'libdir') / 'vapoursynth'
else
  vapoursynth_dep = dependency('', required: false)
  install_dir = get_option('libdir') / 'vapoursynth'
endif

dependencies = [vapoursynth_dep]
link_args = []

shared_module('it', sources,
  dependencies: dependencies,
  cpp_args: opt_cflags + opt_defines,
  c_args: opt_cflags + opt_defines,
  link_args: link_args,
  install: true,
  install_dir: install_dir,
  gnu_symbol_visibility: 'hidden'
)