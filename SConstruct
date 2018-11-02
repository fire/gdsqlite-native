#!python
import os

# Environment
env = Environment(ENV = os.environ);

# Platform & bits
platform = ARGUMENTS.get('p', ARGUMENTS.get('platform', 'windows'));
bits = ARGUMENTS.get('b', ARGUMENTS.get('bits', '64'));
target = ARGUMENTS.get('t', ARGUMENTS.get('target', 'release'));
use_mingw = ARGUMENTS.get('use_mingw', False);
use_osxcross = ARGUMENTS.get('use_osxcross', False)
osxcross_dir = ARGUMENTS.get('osxcross_dir', '.')
output = 'gdsqlite';
godotcpp_lib = 'libgodot-cpp'

if platform == 'linux':
	if ARGUMENTS.get('use_llvm', 'no') == 'yes':
		env['CXX'] = 'clang++';
	else:
		env['CXX']='g++';
	
	if target == 'debug':
		env.Append(CCFLAGS = ['-Og']);
	else:
		env.Append(CCFLAGS = ['-O3']);
	
	if use_mingw:
		env = env.Clone(tools = ['mingw'])
#		env['ENV'] = {'PATH' : os.environ['PATH'], 'TMP' : os.environ['TMP']}
		# Workaround for MinGW. See:
		# http://www.scons.org/wiki/LongCmdLinesOnWin32
#		use_windows_spawn_fix(env)

		mingw_prefix = ''
		# MinGW
		if bits == '64':
			mingw_prefix = 'x86_64-w64-mingw32-'
		elif bits == '32':
			mingw_prefix = 'i686-w64-mingw32-'
		
        env["CC"] = mingw_prefix + 'gcc'
		env['AS'] = mingw_prefix + 'as'
		env['CXX'] = mingw_prefix + 'g++'
		env['AR'] = mingw_prefix + 'gcc-ar'
		env['RANLIB'] = mingw_prefix + 'gcc-ranlib'
		env['LINK'] = mingw_prefix + 'g++'

		env['CCFLAGS'] = ['-g', '-O3', '-std=c++14', '-DSQLITE_OS_WIN=1', '-Wwrite-strings']
		env['LINKFLAGS'] = ['--static', '-Wl,--subsystem,windows', '-Wl,--no-undefined', '-static-libgcc', '-static-libstdc++']

	if bits == '64':
		env.Append(CCFLAGS = [ '-m64' ]);
		env.Append(LINKFLAGS = [ '-m64' ]);
		godotcpp_lib += '.linux.' + target + '.' + bits;
	else:
		env.Append(CCFLAGS = [ '-m32' ]);
		env.Append(LINKFLAGS = [ '-m32' ]);
		godotcpp_lib += '.linux.' + target + '.' + bits;

if platform == 'windows':
	if bits == '64':
		env = Environment(ENV = os.environ, TARGET_ARCH='amd64');
		godotcpp_lib += '.windows.' + target + '.' + bits;
	else:
		env = Environment(ENV = os.environ, TARGET_ARCH='x86');
		godotcpp_lib += '.windows.' + target + '.' + bits;
	
	if target == 'debug':
		env['CCPDBFLAGS'] = '/Zi /Fd${TARGET}.pdb'
		env['PDB']='${TARGET.base}.pdb'
		env.Append(CCFLAGS = ['-D_WIN32', '-EHsc', '/DEBUG', '-D_DEBUG', '/MDd'])
	else:
		env.Append(CCFLAGS = ['-D_WIN32', '/EHsc', '/O2', '/MD' ])
        if use_mingw:
                env = env.Clone(tools = ['mingw'])
#               env['ENV'] = {'PATH' : os.environ['PATH'], 'TMP' : os.environ['TMP']}
                # Workaround for MinGW. See:
                # http://www.scons.org/wiki/LongCmdLinesOnWin32
#               use_windows_spawn_fix(env)

                mingw_prefix = ''
                # MinGW
                if bits == '64':
                        mingw_prefix = 'x86_64-w64-mingw32-'
                elif bits == '32':
                        mingw_prefix = 'i686-w64-mingw32-'

                env["CC"] = mingw_prefix + 'gcc'              
                env['AS'] = mingw_prefix + 'as'
                env['CXX'] = mingw_prefix + 'g++'
                env['AR'] = mingw_prefix + 'gcc-ar'
                env['RANLIB'] = mingw_prefix + 'gcc-ranlib'
                env['LINK'] = mingw_prefix + 'g++'

                env['CCFLAGS'] = ['-g', '-O3', '-std=c++14', '-DSQLITE_OS_WIN=1', '-Wwrite-strings']
                env['LINKFLAGS']= ['--static', '-Wl,--subsystem,windows', '-Wl,--no-undefined', '-static-libgcc', '-static-libstdc++']
    
	if use_mingw:
            env = env.Clone(tools = ['mingw'])
#           env['ENV'] = {'PATH' : os.environ['PATH'], 'TMP' : os.environ['TMP']}
            # Workaround for MinGW. See:
            # http://www.scons.org/wiki/LongCmdLinesOnWin32
#               use_windows_spawn_fix(env)

            mingw_prefix = ''
            # MinGW
            if bits == '64':
                    mingw_prefix = 'x86_64-w64-mingw32-'
            elif bits == '32':
                    mingw_prefix = 'i686-w64-mingw32-'

            env["CC"] = mingw_prefix + 'gcc'
            env['AS'] = mingw_prefix + 'as'
            env['CXX'] = mingw_prefix + 'g++'
            env['AR'] = mingw_prefix + 'gcc-ar'
            env['RANLIB'] = mingw_prefix + 'gcc-ranlib'
            env['LINK'] = mingw_prefix + 'g++'

            env['CCFLAGS'] = ['-g', '-O3', '-std=c++14', '-DSQLITE_OS_WIN=1', '-Wwrite-strings']
            env['LINKFLAGS']= ['--static', '-Wl,--subsystem,windows', '-Wl,--no-undefined', '-static-libgcc', '-static-libstdc++']

output += '.' + platform
if bits == '64':
	output += '.64';
else:
	output += '.32';

# Include dir
env.Append(CPPPATH=[
	'src',
	'thirdparty/sqlite',
	'thirdparty/godot_cpp/godot_headers/',
	'thirdparty/godot_cpp/include',
	'thirdparty/godot_cpp/include/core',
	'thirdparty/godot_cpp/include/gen'
]);

if platform == 'osx' or use_osxcross:
    if bits == '32':
        raise ValueError('Only 64-bit builds are supported for the macOS target.')

    platform = 'osx'

    if osxcross_dir:
        env['CXX'] = osxcross_dir + '/target/bin/x86_64-apple-darwin15-clang++-libc++'
        env['CC'] = osxcross_dir + '/target/bin/x86_64-apple-darwin15-clang'   
        env['LINK'] = osxcross_dir + '/target/bin/x86_64-apple-darwin15-clang++-libc++'
        env['AR'] = osxcross_dir + '/target/bin/x86_64-apple-darwin15-ar'
        env['AS'] = osxcross_dir + '/target/bin/x86_64-apple-darwin15-as'
        env['RANLIB'] = osxcross_dir + '/target/bin/x86_64-apple-darwin15-ranlib'

    env.Append(CCFLAGS=['-g', '-arch', 'x86_64'])
    env.Append(LINKFLAGS=['-arch', 'x86_64', '-framework', 'Cocoa', '-Wl,-undefined,dynamic_lookup'])

    godotcpp_lib += '.osx.' + target + '.' + bits;

    if target == 'debug':
        env.Append(CCFLAGS=['-Og'])
    elif target == 'release':
        env.Append(CCFLAGS=['-O3'])

# Source lists
sources = [
	'src/gdsqlite.cpp',
	'src/library.cpp',
	'thirdparty/sqlite/sqlite3.c',
	'thirdparty/sqlite/spmemvfs.c'
]

# Libraries
env.Append(LIBPATH=['thirdparty/godot_cpp/bin/'])
env.Append(LIBS=[godotcpp_lib])
library = env.SharedLibrary(target='bin/' + output, source=sources)
Install('demo/addons/gdsqlite', source=library)
