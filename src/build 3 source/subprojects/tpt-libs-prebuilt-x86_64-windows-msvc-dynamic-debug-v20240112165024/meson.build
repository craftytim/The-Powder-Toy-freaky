project('tpt-libs-prebuilt', [ 'c', 'cpp' ])

host_arch = 'x86_64'
host_platform = 'windows'
host_libc = 'msvc'
static_dynamic = 'dynamic'
debug_release = 'debug'

bzip2_version = '1.0.8-tpt-libs'
jsoncpp_version = '1.9.5-tpt-libs'
zlib_version = '1.2.11-tpt-libs'
curl_version = '7.86.0+nghttp2-1.50.0+zlib-1.2.11+mbedtls-3.2.1-tpt-libs'
libpng_version = '1.6.37+zlib-1.2.11-tpt-libs'
sdl2_version = '2.24.2-tpt-libs'
fftw_version = '3.3.8-tpt-libs'
lua51_version = '5.1.5-tpt-libs'
lua52_version = '5.2.4-tpt-libs'
luajit_version = '2.1.0-beta3-tpt-libs'

# if this is the first line in this file, then you're looking at an incomplete,
# unconfigured meson.build with its head missing. you'll find its head in build.sh.
is_static = static_dynamic == 'static'
is_debug = debug_release == 'debug'

c_compiler = meson.get_compiler('c')
cpp_compiler = meson.get_compiler('cpp')
config_dlls_pre = []

lib_dir = 'lib'
dll_prefix = ''
if host_platform == 'windows' and host_libc == 'mingw' and not is_static
	lib_dir = 'bin'
	dll_prefix = 'lib'
endif

if host_platform != 'emscripten'
	luajit_name = 'lua'
	if host_platform == 'windows' and not (host_libc == 'mingw' and is_static)
		luajit_name += '51'
	endif
	config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'luajit/bin'), luajit_name + '.dll', 'lua=luajit' ] ]
	meson.override_dependency('luajit', declare_dependency(
		dependencies: [
			c_compiler.find_library(luajit_name, dirs: join_paths(meson.current_source_dir(), 'luajit', lib_dir), static: is_static),
		],
		include_directories: [
			include_directories('luajit/include'),
		],
		version: luajit_version,
	), static: is_static)
endif

lua51_name = 'lua'
if host_platform == 'windows' and host_libc == 'mingw' and not is_static
	lua51_name += '51'
endif
config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'lua5.1/bin'), lua51_name + '.dll', 'lua=lua5.1' ] ]
meson.override_dependency('lua5.1-c++', declare_dependency(
	dependencies: [
		c_compiler.find_library(lua51_name, dirs: join_paths(meson.current_source_dir(), 'lua5.1', lib_dir), static: is_static),
	],
	include_directories: [
		include_directories('lua5.1/include'),
	],
	version: lua51_version,
), static: is_static)

lua52_name = 'lua'
if host_platform == 'windows' and host_libc == 'mingw' and not is_static
	lua52_name += '52'
endif
config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'lua5.2/bin'), lua52_name + '.dll', 'lua=lua5.2' ] ]
meson.override_dependency('lua5.2-c++', declare_dependency(
	dependencies: [
		c_compiler.find_library(lua52_name, dirs: join_paths(meson.current_source_dir(), 'lua5.2', lib_dir), static: is_static),
	],
	include_directories: [
		include_directories('lua5.2/include'),
	],
	version: lua52_version,
), static: is_static)

if host_platform != 'emscripten'
	libcurl_name = 'curl'
	nghttp2_name = 'nghttp2'
	if is_debug
		libcurl_name += '-d'
	endif
	config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'bin'), 'lib' + libcurl_name + '.dll', 'all' ] ]
	config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'bin'), dll_prefix + nghttp2_name + '.dll', 'all' ] ]
	if host_libc == 'msvc' and not is_static
		libcurl_name += '_imp'
	endif
	meson.override_dependency('libcurl', declare_dependency(
		dependencies: [
			c_compiler.find_library(libcurl_name, dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
			c_compiler.find_library(nghttp2_name, dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
			host_platform == 'windows' ? [
				c_compiler.find_library('wsock32'),
				c_compiler.find_library('ws2_32'),
				c_compiler.find_library('crypt32'),
				c_compiler.find_library('bcrypt'),
			] : [],
			host_platform == 'darwin' ? dependency('AppleFrameworks', modules: [
				'Security',
				'SystemConfiguration',
			]) : [],
			(is_static and (host_platform == 'linux' or host_platform == 'windows' or host_platform == 'android')) ? [
				c_compiler.find_library('mbedcrypto', dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
				c_compiler.find_library('mbedtls', dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
				c_compiler.find_library('mbedx509', dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
			] : [],
		],
		include_directories: [
			include_directories('include'),
		],
		version: curl_version,
	), static: is_static)
endif

config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'bin'), dll_prefix + 'fftw3f.dll', 'all' ] ]
meson.override_dependency('fftw3f', declare_dependency(
	dependencies: [
		c_compiler.find_library('fftw3f', dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
	],
	include_directories: [
		include_directories('include'),
	],
	version: fftw_version,
), static: is_static)

if host_platform != 'emscripten'
	if host_platform == 'windows'
		zlib_name = 'zlib'
		if is_static
			zlib_name += 'static'
		endif
		if host_libc == 'msvc' and is_debug
			zlib_name += 'd'
		endif
	else
		zlib_name = 'z'
	endif
	config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'bin'), dll_prefix + zlib_name + '.dll', 'all' ] ]
	meson.override_dependency('zlib', declare_dependency(
		dependencies: [
			c_compiler.find_library(zlib_name, dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
		],
		include_directories: [
			include_directories('include'),
		],
		version: zlib_version,
	), static: is_static)
endif

if host_platform != 'emscripten'
	sdl2_name = 'SDL2'
	sdl2main_name = 'SDL2main'
	if is_static and host_libc == 'msvc'
		sdl2_name += '-static'
	endif
	if is_debug and host_platform != 'android'
		sdl2_name += 'd'
		sdl2main_name += 'd'
	endif
	config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'bin'), sdl2_name + '.dll', 'all' ] ]
	meson.override_dependency('sdl2', declare_dependency(
		dependencies: [
			c_compiler.find_library(sdl2_name, dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
			c_compiler.find_library(sdl2main_name, dirs: join_paths(meson.current_source_dir(), 'lib')),
			host_platform == 'windows' ? [
				c_compiler.find_library('version'),
				c_compiler.find_library('imm32'),
				c_compiler.find_library('winmm'),
			] : [],
			host_platform == 'linux' ? [
				c_compiler.find_library('dl'),
			] : [],
			host_platform == 'android' ? [
				c_compiler.find_library('dl'),
				c_compiler.find_library('android'),
				c_compiler.find_library('log'),
				c_compiler.find_library('GLESv2'),
				c_compiler.find_library('GLESv1_CM'),
			] : [],
			host_platform == 'darwin' ? dependency('AppleFrameworks', modules: [
				'Cocoa',
				'Metal',
				'IOKit',
				'CoreVideo',
				'Carbon',
			]) : [],
			'@0@-@1@'.format(host_platform, host_libc) == 'windows-mingw' ? [
				c_compiler.find_library('mingw32'),
			] : [],
		],
		include_directories: [
			include_directories('include/SDL2'),
		],
		version: sdl2_version,
	), static: is_static)
endif

if host_platform != 'emscripten'
	if host_platform == 'windows'
		png_name = 'png16'
		if is_static and host_libc == 'msvc'
			png_name += '_static'
		endif
	else
		png_name = 'png16'
	endif
	if is_debug
		png_name += 'd'
	endif
	config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'bin'), 'lib' + png_name + '.dll', 'all' ] ]
	meson.override_dependency('libpng16', declare_dependency(
		dependencies: [
			c_compiler.find_library(png_name, dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
		],
		include_directories: [
			include_directories('include'),
		],
		version: libpng_version,
	), static: is_static)
endif

jsoncpp_name = 'jsoncpp'
config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'bin'), dll_prefix + jsoncpp_name + '.dll', 'all' ] ]
meson.override_dependency('jsoncpp', declare_dependency(
	dependencies: [
		cpp_compiler.find_library(jsoncpp_name, dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
	],
	include_directories: [
		include_directories('include'),
	],
	version: jsoncpp_version,
), static: is_static)

if host_platform != 'emscripten'
	bzip2_name = 'bz2'
	config_dlls_pre += [ [ join_paths(meson.current_source_dir(), 'bin'), dll_prefix + bzip2_name + '.dll', 'all' ] ]
	meson.override_dependency('bzip2', declare_dependency(
		dependencies: [
			c_compiler.find_library(bzip2_name, dirs: join_paths(meson.current_source_dir(), lib_dir), static: is_static),
		],
		include_directories: [
			include_directories('include'),
		],
		version: bzip2_version,
	), static: is_static)
endif

if host_platform == 'android'
	sdl_jar = files('lib/sdl.jar')
endif
if host_platform == 'windows' and not is_static
	config_dlls = []
	foreach dll : config_dlls_pre
		config_dlls += [ [ files(join_paths(meson.current_source_dir(), dll[0], dll[1])), dll[1], dll[2] ] ]
	endforeach
endif
