project('libsrtp2', ['c'], license : 'BSD', version: '2.2.0-pre')

libsrtp_files = files(
    'srtp/srtp.c',
    'srtp/ekt.c',
    'crypto/kernel/err.c',
    'crypto/kernel/crypto_kernel.c',
    'crypto/kernel/alloc.c',
    'crypto/kernel/key.c',
    'crypto/cipher/aes_gcm_ossl.c',
    'crypto/cipher/aes_icm_ossl.c',
    'crypto/cipher/null_cipher.c',
    'crypto/cipher/cipher.c',
    'crypto/replay/ut_sim.c',
    'crypto/replay/rdb.c',
    'crypto/replay/rdbx.c',
    'crypto/hash/null_auth.c',
    'crypto/hash/hmac_ossl.c',
    'crypto/hash/auth.c',
    'crypto/math/stat.c',
    'crypto/math/datatypes.c',
)

dep_openssl = dependency('openssl', optional: true)

if not dep_openssl.found()
  libsrtp_files += files(
    'crypto/cipher/aes_icm.c',
    'crypto/cipher/aes.c',
    'crypto/hash/sha1.c',
    'crypto/hash/hmac.c',
  )
endif


internal_inc = include_directories('crypto/include')
libsrtp_inc = include_directories('include')
config_inc = include_directories('.')

compiler = meson.get_compiler('c')


conf = configuration_data()

arch = host_machine.cpu_family()

if arch == 'x86' or arch == 'x86_64'
    conf.set('CPU_CISC', true)
    conf.set('HAVE_X86', true)
else
    conf.set('CPU_RISC', true)
endif

conf.set('OPENSSL', dep_openssl.found())

conf.set('HAVE_INT8_T', compiler.has_type('int8_t'))
conf.set('HAVE_INT16_T', compiler.has_type('int16_t'))
conf.set('HAVE_INT32_T', compiler.has_type('int32_t'))
conf.set('HAVE_UINT8_T', compiler.has_type('uint8_t'))
conf.set('HAVE_UINT16_T', compiler.has_type('uint16_t'))
conf.set('HAVE_UINT32_T', compiler.has_type('uint32_t'))
conf.set('HAVE_UINT64_T', compiler.has_type('uint64_t'))

conf.set('HAVE_ARPA_INET_H', compiler.has_header('arpa/inet.h'))
conf.set('HAVE_BYTESWAP_H', compiler.has_header('byteswap.h'))
conf.set('HAVE_INTTYPES_H', compiler.has_header('inttypes.h'))
conf.set('HAVE_MEMORY_H', compiler.has_header('memory.h'))
conf.set('HAVE_NETINET_IN_H', compiler.has_header('netinet/in.h'))
conf.set('HAVE_STDINT_H', compiler.has_header('stdint.h'))
conf.set('HAVE_STDLIB_H', compiler.has_header('stdlib.h'))
conf.set('HAVE_STRING_H', compiler.has_header('string.h'))
conf.set('HAVE_STRINGS_H', compiler.has_header('strings.h'))
conf.set('HAVE_SYS_SOCKET_H', compiler.has_header('sys/socket.h'))
conf.set('HAVE_SYS_STAT_H', compiler.has_header('sys/stat.h'))
conf.set('HAVE_SYS_TYPES_H', compiler.has_header('sys/types.h'))
conf.set('HAVE_SYS_UIO_H', compiler.has_header('sys/uio.h'))
conf.set('HAVE_UNISTD_H', compiler.has_header('unistd.h'))

conf.set('HAVE_PCAP', compiler.find_library('pcap', required: false).found())

conf.set('HAVE_INET_ATON', compiler.has_function('inet_aton'))
conf.set('HAVE_SIGACTION', compiler.has_function('sigaction'))
conf.set('HAVE_SOCKET', compiler.has_function('socket'))
conf.set('HAVE_USLEEP', compiler.has_function('usleep'))

conf.set('SIZEOF_UNSIGNED_LONG', compiler.sizeof('unsigned long'))
conf.set('SIZEOF_UNSIGNED_LONG_LONG', compiler.sizeof('unsigned long long'))

conf.set_quoted('PACKAGE_NAME', meson.project_name())
conf.set_quoted('PACKAGE_VERSION', meson.project_version())
conf.set_quoted('PACKAGE_STRING', meson.project_name() + meson.project_version())

configure_file(  output: 'config.h',
                configuration: conf)

libsrtp = static_library(
    'srtp',
    libsrtp_files,
    include_directories: [internal_inc, libsrtp_inc],
    c_args: ['-DHAVE_CONFIG_H', '-O4', '-fexpensive-optimizations', '-funroll-loops']
)

libsrtp_dep = declare_dependency(
    include_directories: [libsrtp_inc, config_inc],
    link_with: libsrtp
)
