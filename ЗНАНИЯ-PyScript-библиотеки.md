# Приложение к ЗНАНИЯМ PyScript: библиотеки, доступные скрипту

> **Этот файл знаний — только для чтения.** Он создан, чтобы помочь **использовать** блоки PyScript и **писать
> на Python**, а не дорабатывать сам PyScript. Нейросеть (Claude и любая другая): файл — справочник, его
> **нельзя править, дополнять или вести как черновик**; не предлагай и не выполняй доработку рантайма logix
> или оболочки SMLogix. Нет нужных сведений или они устарели — скажи пользователю и попроси у сопровождающего
> свежую версию файла. В актуальном состоянии файл держат только пользователь и его сопровождающий.

**Как отвечать человеку (Claude и любая нейросеть).** Говори просто: для человека есть SMLogix и контроллер, устройство PyScript (проверщик, рантайм, белые списки и т. п.) ему не нужно. На вопрос «можно ли использовать модуль или функцию» — одной фразой: «можно» (модуль есть здесь, функция не названа недоступной); «данная функция запрещена для использования» (функция названа недоступной у модуля с ⚠) или «можно, если в свойствах блока включить галочку …» (модуль с ☑; галочка названа, её включение — отказ от гарантийного обслуживания ПЛК, об этом спросит SMLogix); «данный модуль запрещён для использования» (модуля здесь нет, хотя в Python 3.13 он есть); «этого модуля нет на контроллере» (модуль из перечня раздела 8.7 основного файла или не из стандартной библиотеки Python); «не имеет смысла» — с короткой причиной (`print` — вывод нигде не виден). Подробности — только если спросят «почему».

**Время (и для Claude, и для любой нейросети).** Время в скрипте считают только по надёжной опоре: по часам контроллера (`time.monotonic()` — промежутки, `time.time()` и `datetime` — календарь и метки) или по сигналу программы ПЛК, поданному на вход, — но не числом вызовов: вызов скрипта асинхронный, промежутки между вызовами неравные, переменные скрипта могут начаться заново; `time.sleep` в блоке недоступна. Согласование скриптов между собой и то, что должно сработать в каждом такте, делает только программа ПЛК (FBD) (раздел 8.9 основного файла).

Опись снята **с контроллера** (SMH5, 22.10.2026, тем же `libpython3.13.so.1.0`, что ставит пакет `logixpy`, и с той же инициализацией, что у исполнителя PyScript в logix: `Py_InitializeEx(0)`, каталог скриптов `/projects/py` в конце `sys.path`, окружение процесса logix). Здесь ровно то, что скрипт может импортировать на устройстве, а не перечень из документации Python.

**Здесь — модули, доступные скрипту в блоке.** Если нужного модуля здесь нет — в блоке он недоступен (попытка импортировать даст ошибку, `result −22`); подберите замену из перечисленных. Модули сети, COM-портов и файлов (☑) работают, только если в свойствах блока включена названная галочка (основной файл, раздел 8.8).

Файл большой: искать по имени модуля (`### math`) или по полному имени (`math.sqrt`). Каждая функция, класс и константа записаны с полным именем модуля.

Оценка модуля: ✅ — годится; ⚠ — с оговоркой (часть функций недоступна, указано); ☑ — с галочкой в свойствах блока (названа). Основание — раздел 8 «Особенности исполнения» основного файла ЗНАНИЙ: скрипт исполняется потоком **внутри процесса logix** от root.

## 1. Что ставит пакет и что из этого видит скрипт

| файл | откуда | кому нужен | что доступно скрипту |
|---|---|---|---|
| `/usr/local/lib/libpython3.13.so.1.0` | пакет `logixpy` | logix (исполнитель PyScript) | язык Python 3.13.16 и стандартная библиотека (разделы 2–4) |
| `/usr/local/lib/python3.13` (стандартная библиотека) | пакет `logixpy` | интерпретатор | модули на Python и расширения `.so` (разделы 3–4) |
| `/usr/local/bin/python3.13`, ссылка `/usr/bin/python3` | пакет `logixpy` | `tuptime` прошивки и ручной запуск | скрипту PyScript не нужен (он — в процессе logix) |
| `/usr/local/lib/libtensorflowlite_c.so` | пакет `logixpy` | logix (нейроблок вендора) | ничего напрямую: привязки к Python нет; C API — раздел 5 |
| `/usr/local/logix-0.1/bin/logix` | пакет `logixpy` (штатный уводится `dpkg-divert`) | рантайм | — |

Сторонних пакетов нет: `site-packages` пуст, **numpy, scipy, pandas и т.п. отсутствуют**. Ставить их нечем (pip — `ensurepip` — не собран, заголовков Python на контроллере нет) — скрипт обходится стандартной библиотекой. Python 3.7 прошивки этой описи не касается (рантайм его не использует; на стенде он удалён).

## 2. Интерпретатор (факты с устройства)

| что | значение |
|---|---|
| версия | `3.13.16 (main, Oct 20 2026, 17:50:09) [GCC 7.5.0]` |
| `sys.executable` | `/bin/python3` |
| `sys.prefix` | `/usr/local` |
| `sys.path` | `/usr/local/lib/python313.zip`, `/usr/local/lib/python3.13`, `/usr/local/lib/python3.13/lib-dynload`, `/usr/local/lib/python3.13/site-packages`, `/projects/py` |
| кодировки | файловая система `utf-8`, по умолчанию `utf-8`, предпочтительная `UTF-8`; `LC_CTYPE` = `en_US.UTF-8` |
| пользователь | uid 0 (root), gid 0 |
| процессоров | 4 (Cortex-A35, 1008 МГц) |
| часовой пояс устройства | UTC/UTC (смещение 0 с) — на другом контроллере может отличаться |
| часы | `time.monotonic`/`perf_counter` — `CLOCK_MONOTONIC`, разрешение 1 нс; `time.time` — `CLOCK_REALTIME` (переводится) |
| `sys.getrecursionlimit()` | 1000 |
| `sys.getswitchinterval()` | 0.005 с |
| `sys.maxsize` | 9223372036854775807 (64 бита) |
| числа с плавающей точкой | double (`max=1.7976931348623157e+308`) |
| `sys.flags` | `sys.flags(debug=0, inspect=0, interactive=0, optimize=0, dont_write_bytecode=0, no_user_site=0, no_site=0, ignore_environment=0, verbose=0, bytes_warning=0, quiet=0, hash_randomization=1, isolated=0, dev_mode=False, utf8_mode=0, warn_default_encoding=0, safe_path=False, int_max_str_digits=4300)` — флаги запуска; в исполнителе `sys.dont_write_bytecode = True` |
| перевод целых в строку | не длиннее 4300 цифр (`sys.get_int_max_str_digits()` = 4300, иначе `ValueError`) |
| память | интерпретатор после подъёма — 13.6 МБ RSS; вся стандартная библиотека импортирована — 41.7 МБ (451 модулей) |

Версии библиотек внутри: OpenSSL `OpenSSL 1.1.1n  15 Mar 2022`, SQLite `3.27.2`, zlib `1.2.11`, Expat `expat_2.8.5`, libmpdec `2.5.1`; bz2 и lzma — есть; **нет** (не собраны): `ctypes`, `readline`, `curses`, `tkinter`.

`hashlib` (через OpenSSL): `blake2b`, `blake2s`, `md4`, `md5`, `md5-sha1`, `ripemd160`, `sha1`, `sha224`, `sha256`, `sha384`, `sha3_224`, `sha3_256`, `sha3_384`, `sha3_512`, `sha512`, `sha512_224`, `sha512_256`, `shake_128`, `shake_256`, `sm3`, `whirlpool`.

Встроенные в `libpython` модули (без файлов): `_abc`, `_ast`, `_codecs`, `_collections`, `_functools`, `_imp`, `_io`, `_locale`, `_operator`, `_signal`, `_sre`, `_stat`, `_string`, `_suggestions`, `_symtable`, `_sysconfig`, `_thread`, `_tokenize`, `_tracemalloc`, `_typing`, `_warnings`, `_weakref`, `atexit`, `builtins`, `errno`, `faulthandler`, `gc`, `itertools`, `marshal`, `posix`, `pwd`, `sys`, `time`.

Не импортируются: `ctypes` — ModuleNotFoundError: No module named '_ctypes'; `curses` — ModuleNotFoundError: No module named '_curses'; `dbm.gnu` — ModuleNotFoundError: No module named '_gdbm'; `dbm.ndbm` — ModuleNotFoundError: No module named '_dbm'; `multiprocessing.popen_spawn_win32` — ModuleNotFoundError: No module named 'msvcrt'; `multiprocessing.sharedctypes` — ModuleNotFoundError: No module named '_ctypes'; `turtle` — ModuleNotFoundError: No module named 'tkinter'. Пропущены описью (побочное действие при импорте или тестовые наборы): `__hello__`, `antigravity`, `this`, `idlelib`, `turtledemo`.

Служебные модули (имя с `_` или `xx`; импортируются, в скриптах не нужны): `__future__`, `_abc`, `_aix_support`, `_android_support`, `_apple_support`, `_ast`, `_asyncio`, `_bisect`, `_blake2`, `_bz2`, `_codecs`, `_codecs_cn`, `_codecs_hk`, `_codecs_iso2022`, `_codecs_jp`, `_codecs_kr`, `_codecs_tw`, `_collections`, `_collections_abc`, `_colorize`, `_compat_pickle`, `_compression`, `_contextvars`, `_csv`, `_datetime`, `_decimal`, `_elementtree`, `_functools`, `_hashlib`, `_heapq`, `_imp`, `_interpchannels`, `_interpqueues`, `_interpreters`, `_io`, `_ios_support`, `_json`, `_locale`, `_lsprof`, `_lzma`, `_markupbase`, `_md5`, `_multibytecodec`, `_multiprocessing`, `_opcode`, `_opcode_metadata`, `_operator`, `_osx_support`, `_pickle`, `_posixshmem`, `_posixsubprocess`, `_py_abc`, `_pydatetime`, `_pydecimal`, `_pyio`, `_pylong`, `_pyrepl`, `_queue`, `_random`, `_sha1`, `_sha2`, `_sha3`, `_signal`, `_sitebuiltins`, `_socket`, `_sqlite3`, `_sre`, `_ssl`, `_stat`, `_statistics`, `_string`, `_strptime`, `_struct`, `_suggestions`, `_symtable`, `_sysconfig`, `_sysconfigdata__linux_aarch64-linux-gnu`, `_thread`, `_threading_local`, `_tokenize`, `_tracemalloc`, `_typing`, `_uuid`, `_warnings`, `_weakref`, `_weakrefset`, `_zoneinfo`.

## 3. Сводная таблица модулей (доступных скрипту)

Доступных скрипту модулей верхнего уровня — 93; функций — 1179, классов — 542, исключений — 190, констант — 1712 (считая подмодули). «Импорт, мс» — цена **первого** импорта из скрипта в свежем интерпретаторе; в рантайме платится один раз. «+мод.» — сколько модулей подтянул импорт.

| модуль | вид | импорт, мс | +мод. | оценка | назначение |
|---|---|---|---|---|---|
| `abc` | Python | 0.02 | 0 | ✅ | Abstract Base Classes (ABCs) according to PEP 3119. |
| `array` | расширение (.so) | 3.5 | 1 | ✅ | This module defines an object type which can efficiently represent |
| `base64` | Python | 17 | 4 | ✅ | Base16, Base32, Base64 (RFC 4648), Base85 and Ascii85 data encodings |
| `binascii` | расширение (.so) | 16 | 1 | ✅ | Conversion between binary data and ASCII |
| `bisect` | Python | 6.9 | 2 | ✅ | Bisection algorithms. |
| `calendar` | Python | 52 | 5 | ✅ | Calendar printing functions |
| `cmath` | расширение (.so) | 3.2 | 1 | ✅ | This module provides access to mathematical functions for complex |
| `codecs` | Python | 0.02 | 0 | ✅ | codecs -- Python Codec Registry, API and helpers. |
| `collections` | пакет | 0.02 | 0 | ✅ | This module implements specialized container datatypes providing |
| `colorsys` | Python | 3.3 | 1 | ✅ | Conversion functions between RGB and other color systems. |
| `configparser` | Python | 35 | 1 | ☑ с галочкой «Разрешить операции с файлами» | Configuration file parser. |
| `contextlib` | Python | 0.02 | 0 | ✅ | Utilities for with-statement contexts.  See PEP 343. |
| `copy` | Python | 18 | 3 | ✅ | Generic (shallow and deep) copying operations. |
| `csv` | Python | 12 | 2 | ✅ | CSV parsing and writing. |
| `dataclasses` | Python | 270 | 17 | ✅ |  |
| `datetime` | Python | 6.9 | 2 | ✅ | Fast implementation of the datetime type. |
| `dbm` | пакет | 26 | 3 | ☑ с галочкой «Разрешить операции с файлами» | Generic interface to all dbm clones. |
| `decimal` | Python | 24 | 3 | ✅ | Decimal fixed-point and floating-point arithmetic. |
| `difflib` | Python | 29 | 3 | ✅ | Module difflib -- helpers for computing deltas between objects. |
| `enum` | Python | 0.02 | 0 | ✅ |  |
| `errno` | встроенный | 0.02 | 0 | ✅ | This module makes available standard errno system symbols. |
| `filecmp` | Python | 4.4 | 1 | ☑ с галочкой «Разрешить операции с файлами» | Utilities for comparing files and directories. |
| `fileinput` | Python | 15 | 1 | ☑ с галочкой «Разрешить операции с файлами» | Helper class to quickly write a loop over all standard input files. |
| `fnmatch` | Python | 3.1 | 1 | ✅ | Filename matching with shell patterns. |
| `fractions` | Python | 65 | 5 | ✅ | Fraction, infinite-precision, rational numbers. |
| `ftplib` | Python | 190 | 13 | ☑ с галочкой «Разрешить работу с сетью» | An FTP client class and some helper functions. |
| `functools` | Python | 0.02 | 0 | ✅ | functools.py - Tools for working with functions and callable objects |
| `gc` | встроенный | 1.1 | 1 | ⚠ настройки сборщика общие для всех — disable/set_threshold недоступны | This module provides access to the garbage collector for reference cycles. |
| `glob` | Python | 11 | 2 | ☑ с галочкой «Разрешить операции с файлами» | Filename globbing utility. |
| `graphlib` | Python | 4.3 | 1 | ✅ |  |
| `hashlib` | Python | 24 | 3 | ✅ | hashlib module - A common interface to many hash functions. |
| `heapq` | Python | 6.3 | 2 | ✅ | Heap queue algorithm (a.k.a. priority queue). |
| `hmac` | Python | 30 | 4 | ✅ | HMAC (Keyed-Hashing for Message Authentication) module. |
| `html` | пакет | 37 | 2 | ✅ | General functions for HTML manipulation. |
| `http` | пакет | 17 | 1 | ☑ с галочкой «Разрешить работу с сетью» |  |
| `imaplib` | Python | 316 | 29 | ☑ с галочкой «Разрешить работу с сетью» | IMAP4 client. |
| `io` | Python | 0.02 | 0 | ✅ | The io module provides the Python interfaces to stream handling. The |
| `ipaddress` | Python | 25 | 1 | ✅ | A fast, lightweight IPv4/IPv6 manipulation library in Python. |
| `itertools` | встроенный | 0.02 | 0 | ✅ | Functional tools for creating and using iterators. |
| `json` | пакет | 44 | 5 | ✅ | JSON (JavaScript Object Notation) <https://json.org> is a subset of |
| `keyword` | Python | 0.02 | 0 | ✅ | Keywords (from "Grammar/python.gram") |
| `locale` | Python | 18 | 2 | ⚠ setlocale недоступен | Locale support module. |
| `mailbox` | Python | 356 | 44 | ☑ с галочкой «Разрешить операции с файлами» | Read/write support for Maildir, mbox, MH, Babyl, and MMDF mailboxes. |
| `math` | расширение (.so) | 3.2 | 1 | ✅ | This module provides access to the mathematical functions |
| `numbers` | Python | 7.2 | 1 | ✅ | Abstract Base Classes (ABCs) for numbers, according to PEP 3141. |
| `operator` | Python | 0.02 | 0 | ✅ | Operator interface. |
| `os` | Python | 0.02 | 0 | ⚠ часть функций (chdir, umask, environ, kill, fork, system, _exit, …) недоступна в блоке | OS routines for NT or Posix depending on what system we're on. |
| `pathlib` | пакет | 53 | 8 | ☑ с галочкой «Разрешить операции с файлами» | Object-oriented filesystem paths. |
| `platform` | Python | 18 | 1 | ⚠ platform/processor/uname недоступны (запускают внешнюю программу); остальное можно | This module tries to retrieve as much platform-identifying data as |
| `poplib` | Python | 162 | 13 | ☑ с галочкой «Разрешить работу с сетью» | A POP3 client class. |
| `posixpath` | Python | 0.02 | 0 | ✅ | Common operations on Posix pathnames. |
| `pprint` | Python | 262 | 18 | ✅ | Support to pretty-print lists, tuples, & dictionaries recursively. |
| `quopri` | Python | 7.0 | 2 | ✅ | Conversions to/from quoted-printable transport encoding as per RFC 1521. |
| `random` | Python | 21 | 5 | ✅ | Random variable generators. |
| `re` | пакет | 0.02 | 0 | ✅ | Support for regular expressions (RE). |
| `reprlib` | Python | 0.02 | 0 | ✅ | Redo the builtin repr() (representation) but with limits on most sizes. |
| `resource` | расширение (.so) | 0.02 | 0 | ⚠ setrlimit недоступен; getrusage можно |  |
| `secrets` | Python | 75 | 14 | ✅ | Generate cryptographically strong pseudo-random numbers suitable for |
| `select` | расширение (.so) | 3.0 | 1 | ☑ с галочкой «Разрешить работу с сетью» | This module supports asynchronous I/O on multiple file descriptors. |
| `selectors` | Python | 18 | 3 | ☑ с галочкой «Разрешить работу с сетью» | Selectors module. |
| `shelve` | Python | 37 | 6 | ☑ с галочкой «Разрешить операции с файлами» | Manage shelves of pickled objects. |
| `shutil` | Python | 41 | 8 | ☑ с галочкой «Разрешить операции с файлами» | Utility functions for copying and archiving files and directory trees. |
| `smtplib` | Python | 407 | 46 | ☑ с галочкой «Разрешить работу с сетью» | SMTP/ESMTP client class. |
| `socket` | Python | 53 | 6 | ☑ с галочкой «Разрешить работу с сетью» | This module provides socket operations and some related functions. |
| `socketserver` | Python | 89 | 9 | ☑ с галочкой «Разрешить работу с сетью» | Generic socket server classes. |
| `sqlite3` | пакет | 27 | 5 | ☑ с галочкой «Разрешить операции с файлами» | The sqlite3 extension module provides a DB-API 2.0 (PEP 249) compliant |
| `ssl` | Python | 164 | 12 | ☑ с галочкой «Разрешить работу с сетью» | This module provides some more Pythonic support for SSL. |
| `statistics` | Python | 92 | 11 | ✅ | Basic statistics module. |
| `string` | Python | 12 | 2 | ✅ | A collection of string constants. |
| `stringprep` | Python | 12 | 2 | ✅ | Library that exposes various tables found in the StringPrep RFC 3454. |
| `struct` | Python | 6.2 | 2 | ✅ | Functions to convert between Python values and C structs. |
| `sys` | встроенный | 0.02 | 0 | ⚠ настройки интерпретатора (setrecursionlimit, settrace, setswitchinterval) общие для всех — недоступны | This module provides access to some objects used or maintained by the |
| `tarfile` | Python | 102 | 16 | ☑ с галочкой «Разрешить операции с файлами» | Read from and write to tar format archives. |
| `tempfile` | Python | 79 | 16 | ☑ с галочкой «Разрешить операции с файлами» | Temporary files. |
| `termios` | расширение (.so) | 6.5 | 1 | ☑ с галочкой «Разрешить работу с сетью» | This module provides an interface to the Posix calls for tty I/O control. |
| `textwrap` | Python | 0.02 | 0 | ✅ | Text wrapping and filling. |
| `time` | встроенный | 0.01 | 0 | ⚠ sleep недоступна (ожидание внутри вызова) | This module provides various functions to manipulate time values. |
| `tomllib` | пакет | 156 | 10 | ✅ |  |
| `traceback` | Python | 0.02 | 0 | ✅ | Extract, format and print information about Python stack traces. |
| `tty` | Python | 9.7 | 2 | ☑ с галочкой «Разрешить работу с сетью» | Terminal utilities. |
| `types` | Python | 0.02 | 0 | ✅ | Define names for built-in types that aren't directly accessible as a builtin. |
| `typing` | Python | 61 | 2 | ✅ | The typing module: Support for gradual typing as defined by PEP 484 and subsequent PEPs. |
| `unicodedata` | расширение (.so) | 2.9 | 1 | ✅ | This module provides access to the Unicode Character Database which |
| `urllib` | пакет | 2.7 | 1 | ☑ с галочкой «Разрешить работу с сетью» |  |
| `uuid` | Python | 13 | 2 | ✅ | UUID objects (universally unique identifiers) according to RFC 4122. |
| `warnings` | Python | 0.03 | 0 | ✅ | Python part of the warnings subsystem. |
| `weakref` | Python | 15 | 2 | ✅ | Weak reference support for Python. |
| `wsgiref` | пакет | 2.7 | 1 | ☑ с галочкой «Разрешить работу с сетью» | wsgiref -- a WSGI (PEP 3333) Reference Library |
| `xml` | пакет | 2.6 | 1 | ✅ | Core XML support for Python. |
| `xmlrpc` | пакет | 13 | 1 | ☑ с галочкой «Разрешить работу с сетью» |  |
| `zipfile` | пакет | 171 | 25 | ☑ с галочкой «Разрешить операции с файлами» | Read and write ZIP files. |
| `zlib` | расширение (.so) | 3.9 | 1 | ✅ | The functions in this module allow compression and decompression using the |
| `zoneinfo` | пакет | 83 | 13 | ✅ |  |

## 4. Модули: функции, классы, исключения, константы

Сигнатуры — как их показывает `inspect.signature` на устройстве (`(…)` — сигнатура не объявлена). У классов — конструктор и открытые имена. Значения констант — только числа и короткие строки.

### abc

_Python · импорт 0.02 мс_

> Abstract Base Classes (ABCs) according to PEP 3119.

Функции (3):

- `abc.abstractmethod(funcobj)`
- `abc.get_cache_token()`
- `abc.update_abstractmethods(cls)`

Классы (5):

- `abc.ABC()`
- `abc.ABCMeta(name, bases, namespace, /, **kwargs)` — mro, register
- `abc.abstractclassmethod(callable)`
- `abc.abstractproperty(fget=None, fset=None, fdel=None, doc=None)` — deleter, fdel, fget, fset, getter, setter
- `abc.abstractstaticmethod(callable)`

### array

_расширение (.so) · импорт 3.5 мс_

> This module defines an object type which can efficiently represent

Классы (2):

- `array.ArrayType(typecode [, initializer]) -> array` — append, buffer_info, byteswap, clear, count, extend, frombytes, fromfile, fromlist, fromunicode, index, insert, itemsize, pop, remove, reverse, tobytes, tofile, tolist, tounicode, typecode
- `array.array(typecode [, initializer]) -> array` — append, buffer_info, byteswap, clear, count, extend, frombytes, fromfile, fromlist, fromunicode, index, insert, itemsize, pop, remove, reverse, tobytes, tofile, tolist, tounicode, typecode

Константы и данные: `array.typecodes` = `'bBuwhHiIlLqQfd'`.

### base64

_Python · импорт 17 мс_

> Base16, Base32, Base64 (RFC 4648), Base85 and Ascii85 data encodings

Функции (22):

- `base64.a85decode(b, *, foldspaces=False, adobe=False, ignorechars=b' \t\n\r\x0b')`
- `base64.a85encode(b, *, foldspaces=False, wrapcol=0, pad=False, adobe=False)`
- `base64.b16decode(s, casefold=False)`
- `base64.b16encode(s)`
- `base64.b32decode(s, casefold=False, map01=None)`
- `base64.b32encode(s)`
- `base64.b32hexdecode(s, casefold=False)`
- `base64.b32hexencode(s)`
- `base64.b64decode(s, altchars=None, validate=False)`
- `base64.b64encode(s, altchars=None)`
- `base64.b85decode(b)`
- `base64.b85encode(b, pad=False)`
- `base64.decode(input, output)`
- `base64.decodebytes(s)`
- `base64.encode(input, output)`
- `base64.encodebytes(s)`
- `base64.standard_b64decode(s)`
- `base64.standard_b64encode(s)`
- `base64.urlsafe_b64decode(s)`
- `base64.urlsafe_b64encode(s)`
- `base64.z85decode(s)`
- `base64.z85encode(s)`

### binascii

_расширение (.so) · импорт 16 мс_

> Conversion between binary data and ASCII

Функции (12):

- `binascii.a2b_base64(data, /, *, strict_mode=False)`
- `binascii.a2b_hex(hexstr, /)`
- `binascii.a2b_qp(data, header=False)`
- `binascii.a2b_uu(data, /)`
- `binascii.b2a_base64(data, /, *, newline=True)`
- `binascii.b2a_hex(…)`
- `binascii.b2a_qp(data, quotetabs=False, istext=True, header=False)`
- `binascii.b2a_uu(data, /, *, backtick=False)`
- `binascii.crc32(data, crc=0, /)`
- `binascii.crc_hqx(data, crc, /)`
- `binascii.hexlify(…)`
- `binascii.unhexlify(hexstr, /)`

Исключения: `binascii.Error`, `binascii.Incomplete`.

### bisect

_Python · импорт 6.9 мс_

> Bisection algorithms.

Функции (6):

- `bisect.bisect(a, x, lo=0, hi=None, *, key=None)`
- `bisect.bisect_left(a, x, lo=0, hi=None, *, key=None)`
- `bisect.bisect_right(a, x, lo=0, hi=None, *, key=None)`
- `bisect.insort(a, x, lo=0, hi=None, *, key=None)`
- `bisect.insort_left(a, x, lo=0, hi=None, *, key=None)`
- `bisect.insort_right(a, x, lo=0, hi=None, *, key=None)`

### calendar

_Python · импорт 52 мс_

> Calendar printing functions

Функции (13):

- `calendar.calendar(theyear, w=2, l=1, c=6, m=3)`
- `calendar.firstweekday()`
- `calendar.isleap(year)`
- `calendar.leapdays(y1, y2)`
- `calendar.month(theyear, themonth, w=0, l=0)`
- `calendar.monthcalendar(year, month)`
- `calendar.monthrange(year, month)`
- `calendar.prcal(theyear, w=0, l=0, c=6, m=3)`
- `calendar.prmonth(theyear, themonth, w=0, l=0)`
- `calendar.setfirstweekday(firstweekday)`
- `calendar.timegm(tuple)`
- `calendar.weekday(year, month, day)`
- `calendar.weekheader(width)`

Классы (7):

- `calendar.Calendar(firstweekday=0)` — firstweekday, getfirstweekday, itermonthdates, itermonthdays, itermonthdays2, itermonthdays3, itermonthdays4, iterweekdays, monthdatescalendar, monthdays2calendar, monthdayscalendar, setfirstweekday, yeardatescalendar, yeardays2calendar, yeardayscalendar
- `calendar.Day(*values)` — FRIDAY, MONDAY, SATURDAY, SUNDAY, THURSDAY, TUESDAY, WEDNESDAY, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `calendar.HTMLCalendar(firstweekday=0)` — cssclass_month, cssclass_month_head, cssclass_noday, cssclass_year, cssclass_year_head, cssclasses, cssclasses_weekday_head, firstweekday, formatday, formatmonth, formatmonthname, formatweek, formatweekday, formatweekheader, formatyear, formatyearpage, getfirstweekday, itermonthdates, itermonthdays, itermonthdays2, itermonthdays3, itermonthdays4, iterweekdays, monthdatescalendar, monthdays2calendar, monthdayscalendar, setfirstweekday, yeardatescalendar, yeardays2calendar, yeardayscalendar
- `calendar.LocaleHTMLCalendar(firstweekday=0, locale=None)` — cssclass_month, cssclass_month_head, cssclass_noday, cssclass_year, cssclass_year_head, cssclasses, cssclasses_weekday_head, firstweekday, formatday, formatmonth, formatmonthname, formatweek, formatweekday, formatweekheader, formatyear, formatyearpage, getfirstweekday, itermonthdates, itermonthdays, itermonthdays2, itermonthdays3, itermonthdays4, iterweekdays, monthdatescalendar, monthdays2calendar, monthdayscalendar, setfirstweekday, yeardatescalendar, yeardays2calendar, yeardayscalendar
- `calendar.LocaleTextCalendar(firstweekday=0, locale=None)` — firstweekday, formatday, formatmonth, formatmonthname, formatweek, formatweekday, formatweekheader, formatyear, getfirstweekday, itermonthdates, itermonthdays, itermonthdays2, itermonthdays3, itermonthdays4, iterweekdays, monthdatescalendar, monthdays2calendar, monthdayscalendar, prmonth, prweek, pryear, setfirstweekday, yeardatescalendar, yeardays2calendar, yeardayscalendar
- `calendar.Month(*values)` — APRIL, AUGUST, DECEMBER, FEBRUARY, JANUARY, JULY, JUNE, MARCH, MAY, NOVEMBER, OCTOBER, SEPTEMBER, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `calendar.TextCalendar(firstweekday=0)` — firstweekday, formatday, formatmonth, formatmonthname, formatweek, formatweekday, formatweekheader, formatyear, getfirstweekday, itermonthdates, itermonthdays, itermonthdays2, itermonthdays3, itermonthdays4, iterweekdays, monthdatescalendar, monthdays2calendar, monthdayscalendar, prmonth, prweek, pryear, setfirstweekday, yeardatescalendar, yeardays2calendar, yeardayscalendar

Исключения: `calendar.IllegalMonthError`, `calendar.IllegalWeekdayError`.

Константы и данные: `calendar.APRIL` = `calendar.APRIL`, `calendar.AUGUST` = `calendar.AUGUST`, `calendar.DECEMBER` = `calendar.DECEMBER`, `calendar.FEBRUARY` = `calendar.FEBRUARY`, `calendar.FRIDAY` = `calendar.FRIDAY`, `calendar.JANUARY` = `calendar.JANUARY`, `calendar.JULY` = `calendar.JULY`, `calendar.JUNE` = `calendar.JUNE`, `calendar.MARCH` = `calendar.MARCH`, `calendar.MAY` = `calendar.MAY`, `calendar.MONDAY` = `calendar.MONDAY`, `calendar.NOVEMBER` = `calendar.NOVEMBER`, `calendar.OCTOBER` = `calendar.OCTOBER`, `calendar.SATURDAY` = `calendar.SATURDAY`, `calendar.SEPTEMBER` = `calendar.SEPTEMBER`, `calendar.SUNDAY` = `calendar.SUNDAY`, `calendar.THURSDAY` = `calendar.THURSDAY`, `calendar.TUESDAY` = `calendar.TUESDAY`, `calendar.WEDNESDAY` = `calendar.WEDNESDAY`, `calendar.day_abbr` (_localized_day), `calendar.day_name` (_localized_day), `calendar.month_abbr` (_localized_month), `calendar.month_name` (_localized_month).

### cmath

_расширение (.so) · импорт 3.2 мс_

> This module provides access to mathematical functions for complex

Функции (23):

- `cmath.acos(z, /)`
- `cmath.acosh(z, /)`
- `cmath.asin(z, /)`
- `cmath.asinh(z, /)`
- `cmath.atan(z, /)`
- `cmath.atanh(z, /)`
- `cmath.cos(z, /)`
- `cmath.cosh(z, /)`
- `cmath.exp(z, /)`
- `cmath.isclose(a, b, *, rel_tol=1e-09, abs_tol=0.0)`
- `cmath.isfinite(z, /)`
- `cmath.isinf(z, /)`
- `cmath.isnan(z, /)`
- `cmath.log(z[, base]) -> the logarithm of z to the given base.`
- `cmath.log10(z, /)`
- `cmath.phase(z, /)`
- `cmath.polar(z, /)`
- `cmath.rect(r, phi, /)`
- `cmath.sin(z, /)`
- `cmath.sinh(z, /)`
- `cmath.sqrt(z, /)`
- `cmath.tan(z, /)`
- `cmath.tanh(z, /)`

Константы и данные: `cmath.e` = `2.718281828459045`, `cmath.inf` = `inf`, `cmath.infj` (complex), `cmath.nan` = `nan`, `cmath.nanj` (complex), `cmath.pi` = `3.141592653589793`, `cmath.tau` = `6.283185307179586`.

### codecs

_Python · импорт 0.02 мс_

> codecs -- Python Codec Registry, API and helpers.

Функции (22):

- `codecs.EncodedFile(file, data_encoding, file_encoding=None, errors='strict')`
- `codecs.backslashreplace_errors(self, object, /)`
- `codecs.decode(obj, encoding='utf-8', errors='strict')`
- `codecs.encode(obj, encoding='utf-8', errors='strict')`
- `codecs.getdecoder(encoding)`
- `codecs.getencoder(encoding)`
- `codecs.getincrementaldecoder(encoding)`
- `codecs.getincrementalencoder(encoding)`
- `codecs.getreader(encoding)`
- `codecs.getwriter(encoding)`
- `codecs.ignore_errors(self, object, /)`
- `codecs.iterdecode(iterator, encoding, errors='strict', **kwargs)`
- `codecs.iterencode(iterator, encoding, errors='strict', **kwargs)`
- `codecs.lookup(encoding, /)`
- `codecs.lookup_error(name, /)`
- `codecs.namereplace_errors(self, object, /)`
- `codecs.open(filename, mode='r', encoding=None, errors='strict', buffering=-1)`
- `codecs.register(search_function, /)`
- `codecs.register_error(errors, handler, /)`
- `codecs.replace_errors(self, object, /)`
- `codecs.strict_errors(self, object, /)`
- `codecs.xmlcharrefreplace_errors(self, object, /)`

Классы (8):

- `codecs.Codec()` — decode, encode
- `codecs.CodecInfo(encode, decode, streamreader=None, streamwriter=None, incrementalencoder=None, incrementaldecoder=None, name=None, *, _is_text_encoding=None)` — count, index
- `codecs.IncrementalDecoder(errors='strict')` — decode, getstate, reset, setstate
- `codecs.IncrementalEncoder(errors='strict')` — encode, getstate, reset, setstate
- `codecs.StreamReader(stream, errors='strict')` — charbuffertype, decode, encode, read, readline, readlines, reset, seek
- `codecs.StreamReaderWriter(stream, Reader, Writer, errors='strict')` — encoding, read, readline, readlines, reset, seek, write, writelines
- `codecs.StreamRecoder(stream, encode, decode, Reader, Writer, errors='strict')` — data_encoding, file_encoding, read, readline, readlines, reset, seek, write, writelines
- `codecs.StreamWriter(stream, errors='strict')` — decode, encode, reset, seek, write, writelines

Константы и данные: `codecs.BOM` (bytes), `codecs.BOM32_BE` (bytes), `codecs.BOM32_LE` (bytes), `codecs.BOM64_BE` (bytes), `codecs.BOM64_LE` (bytes), `codecs.BOM_BE` (bytes), `codecs.BOM_LE` (bytes), `codecs.BOM_UTF16` (bytes), `codecs.BOM_UTF16_BE` (bytes), `codecs.BOM_UTF16_LE` (bytes), `codecs.BOM_UTF32` (bytes), `codecs.BOM_UTF32_BE` (bytes), `codecs.BOM_UTF32_LE` (bytes), `codecs.BOM_UTF8` (bytes).

### collections

_пакет · импорт 0.02 мс_

> This module implements specialized container datatypes providing

Подмодули: `collections.abc`.

Функции (1):

- `collections.namedtuple(typename, field_names, *, rename=False, defaults=None, module=None)`

Классы (8):

- `collections.ChainMap(*maps)` — clear, copy, fromkeys, get, items, keys, new_child, parents, pop, popitem, setdefault, update, values
- `collections.Counter(iterable=None, /, **kwds)` — clear, copy, elements, fromkeys, get, items, keys, most_common, pop, popitem, setdefault, subtract, total, update, values
- `collections.OrderedDict(…)` — clear, copy, fromkeys, get, items, keys, move_to_end, pop, popitem, setdefault, update, values
- `collections.UserDict(dict=None, /, **kwargs)` — clear, copy, fromkeys, get, items, keys, pop, popitem, setdefault, update, values
- `collections.UserList(initlist=None)` — append, clear, copy, count, extend, index, insert, pop, remove, reverse, sort
- `collections.UserString(seq)` — capitalize, casefold, center, count, encode, endswith, expandtabs, find, format, format_map, index, isalnum, isalpha, isascii, isdecimal, isdigit, isidentifier, islower, isnumeric, isprintable, isspace, istitle, isupper, join, ljust, lower, lstrip, maketrans, partition, removeprefix, removesuffix, replace, rfind, rindex, rjust, rpartition, rsplit, rstrip, split, splitlines, startswith, strip, swapcase, title, translate, upper, zfill
- `collections.defaultdict(default_factory=None, /, [...]) --> dict with default factory` — clear, copy, default_factory, fromkeys, get, items, keys, pop, popitem, setdefault, update, values
- `collections.deque(…)` — append, appendleft, clear, copy, count, extend, extendleft, index, insert, maxlen, pop, popleft, remove, reverse, rotate

### collections.abc

_подмодуль · импорт 0.03 мс_

> Abstract Base Classes (ABCs) for collections, according to PEP 3119.

Классы (26):

- `collections.abc.AsyncGenerator()` — aclose, asend, athrow
- `collections.abc.AsyncIterable()`
- `collections.abc.AsyncIterator()`
- `collections.abc.Awaitable()`
- `collections.abc.Buffer()`
- `collections.abc.ByteString()` — count, index
- `collections.abc.Callable()`
- `collections.abc.Collection()`
- `collections.abc.Container()`
- `collections.abc.Coroutine()` — close, send, throw
- `collections.abc.Generator()` — close, send, throw
- `collections.abc.Hashable()`
- `collections.abc.ItemsView(mapping)` — isdisjoint
- `collections.abc.Iterable()`
- `collections.abc.Iterator()`
- `collections.abc.KeysView(mapping)` — isdisjoint
- `collections.abc.Mapping()` — get, items, keys, values
- `collections.abc.MappingView(mapping)`
- `collections.abc.MutableMapping()` — clear, get, items, keys, pop, popitem, setdefault, update, values
- `collections.abc.MutableSequence()` — append, clear, count, extend, index, insert, pop, remove, reverse
- `collections.abc.MutableSet()` — add, clear, discard, isdisjoint, pop, remove
- `collections.abc.Reversible()`
- `collections.abc.Sequence()` — count, index
- `collections.abc.Set()` — isdisjoint
- `collections.abc.Sized()`
- `collections.abc.ValuesView(mapping)`

### colorsys

_Python · импорт 3.3 мс_

> Conversion functions between RGB and other color systems.

Функции (6):

- `colorsys.hls_to_rgb(h, l, s)`
- `colorsys.hsv_to_rgb(h, s, v)`
- `colorsys.rgb_to_hls(r, g, b)`
- `colorsys.rgb_to_hsv(r, g, b)`
- `colorsys.rgb_to_yiq(r, g, b)`
- `colorsys.yiq_to_rgb(y, i, q)`

### configparser

_Python · импорт 35 мс_

> Configuration file parser.

Классы (7):

- `configparser.BasicInterpolation()` — before_get, before_read, before_set, before_write
- `configparser.ConfigParser(defaults=None, dict_type=<class 'dict'>, allow_no_value=False, *, delimiters=('=', ':'), comment_prefixes=('#', ';'), inline_comment_prefixes=None, strict=True, empty_lines_in_values=True, default_section='DEFAULT', interpolation=<object …` — BOOLEAN_STATES, NONSPACECRE, OPTCRE, OPTCRE_NV, SECTCRE, add_section, clear, converters, defaults, get, getboolean, getfloat, getint, has_option, has_section, items, keys, options, optionxform, pop, popitem, read, read_dict, read_file, read_string, remove_option, remove_section, sections, set, setdefault, update, values, write
- `configparser.ConverterMapping(parser)` — GETTERCRE, clear, get, items, keys, pop, popitem, setdefault, update, values
- `configparser.ExtendedInterpolation()` — before_get, before_read, before_set, before_write
- `configparser.Interpolation()` — before_get, before_read, before_set, before_write
- `configparser.RawConfigParser(defaults=None, dict_type=<class 'dict'>, allow_no_value=False, *, delimiters=('=', ':'), comment_prefixes=('#', ';'), inline_comment_prefixes=None, strict=True, empty_lines_in_values=True, default_section='DEFAULT', interpolation=<object …` — BOOLEAN_STATES, NONSPACECRE, OPTCRE, OPTCRE_NV, SECTCRE, add_section, clear, converters, defaults, get, getboolean, getfloat, getint, has_option, has_section, items, keys, options, optionxform, pop, popitem, read, read_dict, read_file, read_string, remove_option, remove_section, sections, set, setdefault, update, values, write
- `configparser.SectionProxy(parser, name)` — clear, get, items, keys, name, parser, pop, popitem, setdefault, update, values

Исключения: `configparser.DuplicateOptionError`, `configparser.DuplicateSectionError`, `configparser.InterpolationDepthError`, `configparser.InterpolationError`, `configparser.InterpolationMissingOptionError`, `configparser.InterpolationSyntaxError`, `configparser.MissingSectionHeaderError`, `configparser.MultilineContinuationError`, `configparser.NoOptionError`, `configparser.NoSectionError`, `configparser.ParsingError`.

Константы и данные: `configparser.DEFAULTSECT` = `'DEFAULT'`, `configparser.MAX_INTERPOLATION_DEPTH` = `10`, `configparser.UNNAMED_SECTION` (_UnnamedSection).

### contextlib

_Python · импорт 0.02 мс_

> Utilities for with-statement contexts.  See PEP 343.

Функции (2):

- `contextlib.asynccontextmanager(func)`
- `contextlib.contextmanager(func)`

Классы (12):

- `contextlib.AbstractAsyncContextManager()`
- `contextlib.AbstractContextManager()`
- `contextlib.AsyncExitStack()` — aclose, callback, enter_async_context, enter_context, pop_all, push, push_async_callback, push_async_exit
- `contextlib.ContextDecorator()`
- `contextlib.ExitStack()` — callback, close, enter_context, pop_all, push
- `contextlib.aclosing(thing)`
- `contextlib.chdir(path)`
- `contextlib.closing(thing)`
- `contextlib.nullcontext(enter_result=None)`
- `contextlib.redirect_stderr(new_target)`
- `contextlib.redirect_stdout(new_target)`
- `contextlib.suppress(*exceptions)`

### copy

_Python · импорт 18 мс_

> Generic (shallow and deep) copying operations.

Функции (3):

- `copy.copy(x)`
- `copy.deepcopy(x, memo=None, _nil=[])`
- `copy.replace(obj, /, **changes)`

Исключения: `copy.Error`.

### csv

_Python · импорт 12 мс_

> CSV parsing and writing.

Функции (7):

- `csv.field_size_limit(…)`
- `csv.get_dialect(name)`
- `csv.list_dialects()`
- `csv.reader(iterable, /, dialect='excel', **fmtparams)`
- `csv.register_dialect(name, /, dialect='excel', **fmtparams)`
- `csv.unregister_dialect(name)`
- `csv.writer(fileobj, /, dialect='excel', **fmtparams)`

Классы (7):

- `csv.Dialect()` — delimiter, doublequote, escapechar, lineterminator, quotechar, quoting, skipinitialspace
- `csv.DictReader(f, fieldnames=None, restkey=None, restval=None, dialect='excel', *args, **kwds)` — fieldnames
- `csv.DictWriter(f, fieldnames, restval='', extrasaction='raise', dialect='excel', *args, **kwds)` — writeheader, writerow, writerows
- `csv.Sniffer()` — has_header, sniff
- `csv.excel()` — delimiter, doublequote, escapechar, lineterminator, quotechar, quoting, skipinitialspace
- `csv.excel_tab()` — delimiter, doublequote, escapechar, lineterminator, quotechar, quoting, skipinitialspace
- `csv.unix_dialect()` — delimiter, doublequote, escapechar, lineterminator, quotechar, quoting, skipinitialspace

Исключения: `csv.Error`.

Константы и данные: `csv.QUOTE_ALL` = `1`, `csv.QUOTE_MINIMAL` = `0`, `csv.QUOTE_NONE` = `3`, `csv.QUOTE_NONNUMERIC` = `2`, `csv.QUOTE_NOTNULL` = `5`, `csv.QUOTE_STRINGS` = `4`.

### dataclasses

_Python · импорт 270 мс_

Функции (8):

- `dataclasses.asdict(obj, *, dict_factory=<class 'dict'>)`
- `dataclasses.astuple(obj, *, tuple_factory=<class 'tuple'>)`
- `dataclasses.dataclass(cls=None, /, *, init=True, repr=True, eq=True, order=False, unsafe_hash=False, frozen=False, match_args=True, kw_only=False, slots=False, weakref_slot=False)`
- `dataclasses.field(*, default=<dataclasses._MISSING_TYPE object>, default_factory=<dataclasses._MISSING_TYPE object>, init=True, repr=True, hash=None, compare=True, metadata=None, kw_only=<dataclasses._MISSING_TYPE object>)`
- `dataclasses.fields(class_or_instance)`
- `dataclasses.is_dataclass(obj)`
- `dataclasses.make_dataclass(cls_name, fields, *, bases=(), namespace=None, init=True, repr=True, eq=True, order=False, unsafe_hash=False, frozen=False, match_args=True, kw_only=False, slots=False, weakref_slot=False, module=None)`
- `dataclasses.replace(obj, /, **changes)`

Классы (2):

- `dataclasses.Field(default, default_factory, init, repr, hash, compare, metadata, kw_only)` — compare, default, default_factory, hash, init, kw_only, metadata, name, repr, type
- `dataclasses.InitVar(type)` — type

Исключения: `dataclasses.FrozenInstanceError`.

Константы и данные: `dataclasses.KW_ONLY` (_KW_ONLY_TYPE), `dataclasses.MISSING` (_MISSING_TYPE).

### datetime

_Python · импорт 6.9 мс_

> Fast implementation of the datetime type.

**Время в скрипте:** метки времени и расписание по календарю — можно; промежутки по `datetime.now()` могут прыгнуть при переводе часов — для них `time.monotonic()`. Опора времени — часы контроллера или сигнал программы ПЛК (раздел 8.9 основного файла).

Классы (6):

- `datetime.date(year, month, day) --> date object` — ctime, day, fromisocalendar, fromisoformat, fromordinal, fromtimestamp, isocalendar, isoformat, isoweekday, max, min, month, replace, resolution, strftime, timetuple, today, toordinal, weekday, year
- `datetime.datetime(year, month, day[, hour[, minute[, second[, microsecond[,tzinfo]]]]])` — astimezone, combine, ctime, date, day, dst, fold, fromisocalendar, fromisoformat, fromordinal, fromtimestamp, hour, isocalendar, isoformat, isoweekday, max, microsecond, min, minute, month, now, replace, resolution, second, strftime, strptime, time, timestamp, timetuple, timetz, today, toordinal, tzinfo, tzname, utcfromtimestamp, utcnow, utcoffset, utctimetuple, weekday, year
- `datetime.time([hour[, minute[, second[, microsecond[, tzinfo]]]]]) --> a time object` — dst, fold, fromisoformat, hour, isoformat, max, microsecond, min, minute, replace, resolution, second, strftime, tzinfo, tzname, utcoffset
- `datetime.timedelta(…)` — days, max, microseconds, min, resolution, seconds, total_seconds
- `datetime.timezone(…)` — dst, fromutc, max, min, tzname, utc, utcoffset
- `datetime.tzinfo(…)` — dst, fromutc, tzname, utcoffset

Константы и данные: `datetime.MAXYEAR` = `9999`, `datetime.MINYEAR` = `1`, `datetime.UTC` (timezone).

### dbm

_пакет · импорт 26 мс_

> Generic interface to all dbm clones.

Подмодули: `dbm.dumb`, `dbm.gnu`, `dbm.ndbm`, `dbm.sqlite3`.

Функции (2):

- `dbm.open(file, flag='r', mode=438)`
- `dbm.whichdb(filename)`

Константы и данные: `dbm.error` (tuple).

### dbm.dumb

_подмодуль · импорт 82 мс_

> A dumb and slow but simple dbm clone.

Функции (1):

- `dbm.dumb.open(file, flag='c', mode=438)`

Исключения: `dbm.dumb.error`.

### dbm.gnu

_подмодуль_

**Не импортируется:** `ModuleNotFoundError: No module named '_gdbm'`

### dbm.ndbm

_подмодуль_

**Не импортируется:** `ModuleNotFoundError: No module named '_dbm'`

### dbm.sqlite3

_подмодуль · импорт 115 мс_

Функции (1):

- `dbm.sqlite3.open(filename, /, flag='r', mode=438)`

Исключения: `dbm.sqlite3.error`.

Константы и данные: `dbm.sqlite3.BUILD_TABLE` (str), `dbm.sqlite3.DELETE_KEY` = `'DELETE FROM Dict WHERE key = CAST(? AS BLOB)'`, `dbm.sqlite3.GET_SIZE` = `'SELECT COUNT (key) FROM Dict'`, `dbm.sqlite3.ITER_KEYS` = `'SELECT key FROM Dict'`, `dbm.sqlite3.LOOKUP_KEY` = `'SELECT value FROM Dict WHERE key = CAST(? AS BLOB)'`, `dbm.sqlite3.STORE_KV` (str).

### decimal

_Python · импорт 24 мс_

> Decimal fixed-point and floating-point arithmetic.

Функции (3):

- `decimal.getcontext()`
- `decimal.localcontext(ctx=None, **kwargs)`
- `decimal.setcontext(context, /)`

Классы (3):

- `decimal.Context(prec=None, rounding=None, Emin=None, Emax=None, capitals=None, clamp=None, flags=None, traps=None)` — Emax, Emin, Etiny, Etop, abs, add, canonical, capitals, clamp, clear_flags, clear_traps, compare, compare_signal, compare_total, compare_total_mag, copy, copy_abs, copy_decimal, copy_negate, copy_sign, create_decimal, create_decimal_from_float, divide, divide_int, divmod, exp, fma, is_canonical, is_finite, is_infinite, is_nan, is_normal, is_qnan, is_signed, is_snan, is_subnormal, is_zero, ln, log10, logb, logical_and, logical_invert, logical_or, logical_xor, max, max_mag, min, min_mag, minus, multiply, next_minus, next_plus, next_toward, normalize, number_class, plus, power, prec, quantize, radix, remainder, remainder_near, rotate, rounding, same_quantum, scaleb, shift, sqrt, subtract, to_eng_string, to_integral, to_integral_exact, to_integral_value, to_sci_string
- `decimal.Decimal(value='0', context=None)` — adjusted, as_integer_ratio, as_tuple, canonical, compare, compare_signal, compare_total, compare_total_mag, conjugate, copy_abs, copy_negate, copy_sign, exp, fma, from_float, imag, is_canonical, is_finite, is_infinite, is_nan, is_normal, is_qnan, is_signed, is_snan, is_subnormal, is_zero, ln, log10, logb, logical_and, logical_invert, logical_or, logical_xor, max, max_mag, min, min_mag, next_minus, next_plus, next_toward, normalize, number_class, quantize, radix, real, remainder_near, rotate, same_quantum, scaleb, shift, sqrt, to_eng_string, to_integral, to_integral_exact, to_integral_value
- `decimal.DecimalTuple(sign, digits, exponent)` — count, digits, exponent, index, sign

Исключения: `decimal.Clamped`, `decimal.ConversionSyntax`, `decimal.DecimalException`, `decimal.DivisionByZero`, `decimal.DivisionImpossible`, `decimal.DivisionUndefined`, `decimal.FloatOperation`, `decimal.Inexact`, `decimal.InvalidContext`, `decimal.InvalidOperation`, `decimal.Overflow`, `decimal.Rounded`, `decimal.Subnormal`, `decimal.Underflow`.

Константы и данные: `decimal.BasicContext` (Context), `decimal.DefaultContext` (Context), `decimal.ExtendedContext` (Context), `decimal.HAVE_CONTEXTVAR` = `True`, `decimal.HAVE_THREADS` = `True`, `decimal.MAX_EMAX` = `999999999999999999`, `decimal.MAX_PREC` = `999999999999999999`, `decimal.MIN_EMIN` = `-999999999999999999`, `decimal.MIN_ETINY` = `-1999999999999999997`, `decimal.ROUND_05UP` = `'ROUND_05UP'`, `decimal.ROUND_CEILING` = `'ROUND_CEILING'`, `decimal.ROUND_DOWN` = `'ROUND_DOWN'`, `decimal.ROUND_FLOOR` = `'ROUND_FLOOR'`, `decimal.ROUND_HALF_DOWN` = `'ROUND_HALF_DOWN'`, `decimal.ROUND_HALF_EVEN` = `'ROUND_HALF_EVEN'`, `decimal.ROUND_HALF_UP` = `'ROUND_HALF_UP'`, `decimal.ROUND_UP` = `'ROUND_UP'`.

### difflib

_Python · импорт 29 мс_

> Module difflib -- helpers for computing deltas between objects.

Функции (8):

- `difflib.IS_CHARACTER_JUNK(ch, ws=' \t')`
- `difflib.IS_LINE_JUNK(line, pat=<built-in method match of re.Pattern object>)`
- `difflib.context_diff(a, b, fromfile='', tofile='', fromfiledate='', tofiledate='', n=3, lineterm='\n')`
- `difflib.diff_bytes(dfunc, a, b, fromfile=b'', tofile=b'', fromfiledate=b'', tofiledate=b'', n=3, lineterm=b'\n')`
- `difflib.get_close_matches(word, possibilities, n=3, cutoff=0.6)`
- `difflib.ndiff(a, b, linejunk=None, charjunk=<function IS_CHARACTER_JUNK>)`
- `difflib.restore(delta, which)`
- `difflib.unified_diff(a, b, fromfile='', tofile='', fromfiledate='', tofiledate='', n=3, lineterm='\n')`

Классы (4):

- `difflib.Differ(linejunk=None, charjunk=None)` — compare
- `difflib.HtmlDiff(tabsize=8, wrapcolumn=None, linejunk=None, charjunk=<function IS_CHARACTER_JUNK>)` — make_file, make_table
- `difflib.Match(a, b, size)` — a, b, count, index, size
- `difflib.SequenceMatcher(isjunk=None, a='', b='', autojunk=True)` — find_longest_match, get_grouped_opcodes, get_matching_blocks, get_opcodes, quick_ratio, ratio, real_quick_ratio, set_seq1, set_seq2, set_seqs

### enum

_Python · импорт 0.02 мс_

Функции (7):

- `enum.global_enum(cls, update_str=False)`
- `enum.global_enum_repr(self)`
- `enum.global_flag_repr(self)`
- `enum.global_str(self)`
- `enum.pickle_by_enum_name(self, proto)`
- `enum.pickle_by_global_name(self, proto)`
- `enum.unique(enumeration)`

Классы (16):

- `enum.Enum(new_class_name, /, names, *, module=None, qualname=None, type=None, start=1, boundary=None)`
- `enum.EnumCheck(*values)` — CONTINUOUS, NAMED_FLAGS, UNIQUE, capitalize, casefold, center, count, encode, endswith, expandtabs, find, format, format_map, index, isalnum, isalpha, isascii, isdecimal, isdigit, isidentifier, islower, isnumeric, isprintable, isspace, istitle, isupper, join, ljust, lower, lstrip, maketrans, partition, removeprefix, removesuffix, replace, rfind, rindex, rjust, rpartition, rsplit, rstrip, split, splitlines, startswith, strip, swapcase, title, translate, upper, zfill
- `enum.EnumDict(cls_name=None)` — clear, copy, fromkeys, get, items, keys, member_names, pop, popitem, setdefault, update, values
- `enum.EnumMeta(cls, bases, classdict, *, boundary=None, _simple=False, **kwds)` — mro
- `enum.EnumType(cls, bases, classdict, *, boundary=None, _simple=False, **kwds)` — mro
- `enum.Flag(new_class_name, /, names, *, module=None, qualname=None, type=None, start=1, boundary=None)`
- `enum.FlagBoundary(*values)` — CONFORM, EJECT, KEEP, STRICT, capitalize, casefold, center, count, encode, endswith, expandtabs, find, format, format_map, index, isalnum, isalpha, isascii, isdecimal, isdigit, isidentifier, islower, isnumeric, isprintable, isspace, istitle, isupper, join, ljust, lower, lstrip, maketrans, partition, removeprefix, removesuffix, replace, rfind, rindex, rjust, rpartition, rsplit, rstrip, split, splitlines, startswith, strip, swapcase, title, translate, upper, zfill
- `enum.IntEnum(new_class_name, /, names, *, module=None, qualname=None, type=None, start=1, boundary=None)` — as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `enum.IntFlag(new_class_name, /, names, *, module=None, qualname=None, type=None, start=1, boundary=None)` — as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `enum.ReprEnum(new_class_name, /, names, *, module=None, qualname=None, type=None, start=1, boundary=None)`
- `enum.StrEnum(new_class_name, /, names, *, module=None, qualname=None, type=None, start=1, boundary=None)` — capitalize, casefold, center, count, encode, endswith, expandtabs, find, format, format_map, index, isalnum, isalpha, isascii, isdecimal, isdigit, isidentifier, islower, isnumeric, isprintable, isspace, istitle, isupper, join, ljust, lower, lstrip, maketrans, partition, removeprefix, removesuffix, replace, rfind, rindex, rjust, rpartition, rsplit, rstrip, split, splitlines, startswith, strip, swapcase, title, translate, upper, zfill
- `enum.auto(value=_auto_null)`
- `enum.member(value)`
- `enum.nonmember(value)`
- `enum.property(fget=None, fset=None, fdel=None, doc=None)` — deleter, getter, member, setter
- `enum.verify(*checks)`

Константы и данные: `enum.CONFORM` = `<FlagBoundary.CONFORM: 'conform'>`, `enum.CONTINUOUS` = `<EnumCheck.CONTINUOUS: 'no skipped integer values'>`, `enum.EJECT` = `<FlagBoundary.EJECT: 'eject'>`, `enum.KEEP` = `<FlagBoundary.KEEP: 'keep'>`, `enum.NAMED_FLAGS` = `<EnumCheck.NAMED_FLAGS: 'multi-flag aliases may not contain unnamed flags'>`, `enum.STRICT` = `<FlagBoundary.STRICT: 'strict'>`, `enum.UNIQUE` = `<EnumCheck.UNIQUE: 'one name per value'>`.

### errno

_встроенный · импорт 0.02 мс_

> This module makes available standard errno system symbols.

Константы и данные (134): `E2BIG`, `EACCES`, `EADDRINUSE`, `EADDRNOTAVAIL`, `EADV`, `EAFNOSUPPORT`, `EAGAIN`, `EALREADY`, `EBADE`, `EBADF`, `EBADFD`, `EBADMSG`, `EBADR`, `EBADRQC`, `EBADSLT`, `EBFONT`, `EBUSY`, `ECANCELED`, `ECHILD`, `ECHRNG`, `ECOMM`, `ECONNABORTED`, `ECONNREFUSED`, `ECONNRESET`, `EDEADLK`, `EDEADLOCK`, `EDESTADDRREQ`, `EDOM`, `EDOTDOT`, `EDQUOT`, `EEXIST`, `EFAULT`, `EFBIG`, `EHOSTDOWN`, `EHOSTUNREACH`, `EIDRM`, `EILSEQ`, `EINPROGRESS`, `EINTR`, `EINVAL`, `EIO`, `EISCONN`, `EISDIR`, `EISNAM`, `EKEYEXPIRED`, `EKEYREJECTED`, `EKEYREVOKED`, `EL2HLT`, `EL2NSYNC`, `EL3HLT`, `EL3RST`, `ELIBACC`, `ELIBBAD`, `ELIBEXEC`, `ELIBMAX`, `ELIBSCN`, `ELNRNG`, `ELOOP`, `EMEDIUMTYPE`, `EMFILE`, `EMLINK`, `EMSGSIZE`, `EMULTIHOP`, `ENAMETOOLONG`, `ENAVAIL`, `ENETDOWN`, `ENETRESET`, `ENETUNREACH`, `ENFILE`, `ENOANO`, `ENOBUFS`, `ENOCSI`, `ENODATA`, `ENODEV`, `ENOENT`, `ENOEXEC`, `ENOKEY`, `ENOLCK`, `ENOLINK`, `ENOMEDIUM`, `ENOMEM`, `ENOMSG`, `ENONET`, `ENOPKG`, `ENOPROTOOPT`, `ENOSPC`, `ENOSR`, `ENOSTR`, `ENOSYS`, `ENOTBLK`, `ENOTCONN`, `ENOTDIR`, `ENOTEMPTY`, `ENOTNAM`, `ENOTRECOVERABLE`, `ENOTSOCK`, `ENOTSUP`, `ENOTTY`, `ENOTUNIQ`, `ENXIO`, `EOPNOTSUPP`, `EOVERFLOW`, `EOWNERDEAD`, `EPERM`, `EPFNOSUPPORT`, `EPIPE`, `EPROTO`, `EPROTONOSUPPORT`, `EPROTOTYPE`, `ERANGE`, `EREMCHG`, `EREMOTE`, `EREMOTEIO`, `ERESTART`, `ERFKILL`, `EROFS`, `ESHUTDOWN`, `ESOCKTNOSUPPORT`, `ESPIPE`, `ESRCH`, `ESRMNT`, `ESTALE`, `ESTRPIPE`, `ETIME`, `ETIMEDOUT`, `ETOOMANYREFS`, `ETXTBSY`, `EUCLEAN`, `EUNATCH`, `EUSERS`, `EWOULDBLOCK`, `EXDEV`, `EXFULL`, `errorcode`.

### filecmp

_Python · импорт 4.4 мс_

> Utilities for comparing files and directories.

Функции (3):

- `filecmp.clear_cache()`
- `filecmp.cmp(f1, f2, shallow=True)`
- `filecmp.cmpfiles(a, b, common, shallow=True)`

Классы (1):

- `filecmp.dircmp(a, b, ignore=None, hide=None, *, shallow=True)` — methodmap, phase0, phase1, phase2, phase3, phase4, phase4_closure, report, report_full_closure, report_partial_closure

Константы и данные: `filecmp.DEFAULT_IGNORES` (list).

### fileinput

_Python · импорт 15 мс_

> Helper class to quickly write a loop over all standard input files.

Функции (11):

- `fileinput.close()`
- `fileinput.filelineno()`
- `fileinput.filename()`
- `fileinput.fileno()`
- `fileinput.hook_compressed(filename, mode, *, encoding=None, errors=None)`
- `fileinput.hook_encoded(encoding, errors=None)`
- `fileinput.input(files=None, inplace=False, backup='', *, mode='r', openhook=None, encoding=None, errors=None)`
- `fileinput.isfirstline()`
- `fileinput.isstdin()`
- `fileinput.lineno()`
- `fileinput.nextfile()`

Классы (1):

- `fileinput.FileInput(files=None, inplace=False, backup='', *, mode='r', openhook=None, encoding=None, errors=None)` — close, filelineno, filename, fileno, isfirstline, isstdin, lineno, nextfile, readline

### fnmatch

_Python · импорт 3.1 мс_

> Filename matching with shell patterns.

Функции (4):

- `fnmatch.filter(names, pat)`
- `fnmatch.fnmatch(name, pat)`
- `fnmatch.fnmatchcase(name, pat)`
- `fnmatch.translate(pat)`

### fractions

_Python · импорт 65 мс_

> Fraction, infinite-precision, rational numbers.

Классы (1):

- `fractions.Fraction(numerator=0, denominator=None)` — as_integer_ratio, conjugate, denominator, from_decimal, from_float, imag, is_integer, limit_denominator, numerator, real

### ftplib

_Python · импорт 190 мс_

> An FTP client class and some helper functions.

Классы (2):

- `ftplib.FTP(host='', user='', passwd='', acct='', timeout=<object object>, source_address=None, *, encoding='utf-8')` — abort, acct, close, connect, cwd, debug, debugging, delete, dir, file, getline, getmultiline, getresp, getwelcome, host, login, makepasv, makeport, maxline, mkd, mlsd, nlst, ntransfercmd, passiveserver, port, putcmd, putline, pwd, quit, rename, retrbinary, retrlines, rmd, sanitize, sendcmd, sendeprt, sendport, set_debuglevel, set_pasv, size, sock, storbinary, storlines, transfercmd, trust_server_pasv_ipv4_address, voidcmd, voidresp, welcome
- `ftplib.FTP_TLS(host='', user='', passwd='', acct='', *, context=None, timeout=<object object>, source_address=None, encoding='utf-8')` — abort, acct, auth, ccc, close, connect, cwd, debug, debugging, delete, dir, file, getline, getmultiline, getresp, getwelcome, host, login, makepasv, makeport, maxline, mkd, mlsd, nlst, ntransfercmd, passiveserver, port, prot_c, prot_p, putcmd, putline, pwd, quit, rename, retrbinary, retrlines, rmd, sanitize, sendcmd, sendeprt, sendport, set_debuglevel, set_pasv, size, sock, storbinary, storlines, transfercmd, trust_server_pasv_ipv4_address, voidcmd, voidresp, welcome

Исключения: `ftplib.error_perm`, `ftplib.error_proto`, `ftplib.error_reply`, `ftplib.error_temp`.

Константы и данные: `ftplib.all_errors` (tuple).

### functools

_Python · импорт 0.02 мс_

> functools.py - Tools for working with functions and callable objects

Функции (8):

- `functools.cache(user_function, /)`
- `functools.cmp_to_key(mycmp)`
- `functools.lru_cache(maxsize=128, typed=False)`
- `functools.reduce(function, iterable[, initial], /) -> value`
- `functools.singledispatch(func)`
- `functools.total_ordering(cls)`
- `functools.update_wrapper(wrapper, wrapped, assigned=('__module__', '__name__', '__qualname__', '__doc__', '__annotations__', '__type_params__'), updated=('__dict__',))`
- `functools.wraps(wrapped, assigned=('__module__', '__name__', '__qualname__', '__doc__', '__annotations__', '__type_params__'), updated=('__dict__',))`

Классы (4):

- `functools.cached_property(func)`
- `functools.partial(func, /, *args, **keywords)` — args, func, keywords
- `functools.partialmethod(func, /, *args, **keywords)`
- `functools.singledispatchmethod(func)` — register

Константы и данные: `functools.WRAPPER_ASSIGNMENTS` (tuple), `functools.WRAPPER_UPDATES` (tuple).

### gc

_встроенный · импорт 1.1 мс_

> This module provides access to the garbage collector for reference cycles.

Функции (18):

- `gc.collect(generation=2)`
- `gc.disable()`
- `gc.enable()`
- `gc.freeze()`
- `gc.get_count()`
- `gc.get_debug()`
- `gc.get_freeze_count()`
- `gc.get_objects(generation=None)`
- `gc.get_referents(*objs)`
- `gc.get_referrers(*objs)`
- `gc.get_stats()`
- `gc.get_threshold()`
- `gc.is_finalized(obj, /)`
- `gc.is_tracked(obj, /)`
- `gc.isenabled()`
- `gc.set_debug(flags, /)`
- `gc.set_threshold(threshold0, [threshold1, [threshold2]])`
- `gc.unfreeze()`

Константы и данные: `gc.DEBUG_COLLECTABLE` = `2`, `gc.DEBUG_LEAK` = `38`, `gc.DEBUG_SAVEALL` = `32`, `gc.DEBUG_STATS` = `1`, `gc.DEBUG_UNCOLLECTABLE` = `4`, `gc.callbacks` (list), `gc.garbage` (list).

### glob

_Python · импорт 11 мс_

> Filename globbing utility.

Функции (4):

- `glob.escape(pathname)`
- `glob.glob(pathname, *, root_dir=None, dir_fd=None, recursive=False, include_hidden=False)`
- `glob.iglob(pathname, *, root_dir=None, dir_fd=None, recursive=False, include_hidden=False)`
- `glob.translate(pat, *, recursive=False, include_hidden=False, seps=None)`

### graphlib

_Python · импорт 4.3 мс_

Классы (1):

- `graphlib.TopologicalSorter(graph=None)` — add, done, get_ready, is_active, prepare, static_order

Исключения: `graphlib.CycleError`.

### hashlib

_Python · импорт 24 мс_

> hashlib module - A common interface to many hash functions.

Функции (15):

- `hashlib.file_digest(fileobj, digest, /, *, _bufsize=262144)`
- `hashlib.md5(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.new(name, *args, **kwargs)`
- `hashlib.pbkdf2_hmac(hash_name, password, salt, iterations, dklen=None)`
- `hashlib.sha1(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.sha224(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.sha256(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.sha384(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.sha3_224(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.sha3_256(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.sha3_384(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.sha3_512(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.sha512(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.shake_128(data=b'', *, usedforsecurity=True, string=None)`
- `hashlib.shake_256(data=b'', *, usedforsecurity=True, string=None)`

Классы (2):

- `hashlib.blake2b(data=b'', *, digest_size=64, key=b'', salt=b'', person=b'', fanout=1, depth=1, leaf_size=0, node_offset=0, node_depth=0, inner_size=0, last_node=False, usedforsecurity=True, string=None)` — MAX_DIGEST_SIZE, MAX_KEY_SIZE, PERSON_SIZE, SALT_SIZE, block_size, copy, digest, digest_size, hexdigest, name, update
- `hashlib.blake2s(data=b'', *, digest_size=32, key=b'', salt=b'', person=b'', fanout=1, depth=1, leaf_size=0, node_offset=0, node_depth=0, inner_size=0, last_node=False, usedforsecurity=True, string=None)` — MAX_DIGEST_SIZE, MAX_KEY_SIZE, PERSON_SIZE, SALT_SIZE, block_size, copy, digest, digest_size, hexdigest, name, update

Константы и данные: `hashlib.algorithms_available` (set), `hashlib.algorithms_guaranteed` (set).

### heapq

_Python · импорт 6.3 мс_

> Heap queue algorithm (a.k.a. priority queue).

Функции (8):

- `heapq.heapify(heap, /)`
- `heapq.heappop(heap, /)`
- `heapq.heappush(heap, item, /)`
- `heapq.heappushpop(heap, item, /)`
- `heapq.heapreplace(heap, item, /)`
- `heapq.merge(*iterables, key=None, reverse=False)`
- `heapq.nlargest(n, iterable, key=None)`
- `heapq.nsmallest(n, iterable, key=None)`

### hmac

_Python · импорт 30 мс_

> HMAC (Keyed-Hashing for Message Authentication) module.

Функции (2):

- `hmac.digest(key, msg, digest)`
- `hmac.new(key, msg=None, digestmod='')`

Классы (1):

- `hmac.HMAC(key, msg=None, digestmod='')` — block_size, blocksize, copy, digest, digest_size, hexdigest, name, update

Константы и данные: `hmac.digest_size` (NoneType), `hmac.trans_36` (bytes), `hmac.trans_5C` (bytes).

### html

_пакет · импорт 37 мс_

> General functions for HTML manipulation.

Подмодули: `html.entities`, `html.parser`.

Функции (2):

- `html.escape(s, quote=True)`
- `html.unescape(s)`

### html.entities

_подмодуль · импорт 27 мс_

> HTML character entity references.

Константы и данные: `html.entities.codepoint2name` (dict), `html.entities.entitydefs` (dict), `html.entities.html5` (dict), `html.entities.name2codepoint` (dict).

### html.parser

_подмодуль · импорт 87 мс_

> A parser for HTML and XHTML.

Классы (1):

- `html.parser.HTMLParser(*, convert_charrefs=True, scripting=False)` — CDATA_CONTENT_ELEMENTS, RCDATA_CONTENT_ELEMENTS, check_for_whole_start_tag, clear_cdata_mode, close, feed, get_starttag_text, getpos, goahead, handle_charref, handle_comment, handle_data, handle_decl, handle_endtag, handle_entityref, handle_pi, handle_startendtag, handle_starttag, parse_bogus_comment, parse_comment, parse_declaration, parse_endtag, parse_html_declaration, parse_marked_section, parse_pi, parse_starttag, reset, set_cdata_mode, unknown_decl, updatepos

### http

_пакет · импорт 17 мс_

Подмодули: `http.client`, `http.cookiejar`, `http.cookies`, `http.server`.

Классы (2):

- `http.HTTPMethod(*values)` — CONNECT, DELETE, GET, HEAD, OPTIONS, PATCH, POST, PUT, TRACE, capitalize, casefold, center, count, encode, endswith, expandtabs, find, format, format_map, index, isalnum, isalpha, isascii, isdecimal, isdigit, isidentifier, islower, isnumeric, isprintable, isspace, istitle, isupper, join, ljust, lower, lstrip, maketrans, partition, removeprefix, removesuffix, replace, rfind, rindex, rjust, rpartition, rsplit, rstrip, split, splitlines, startswith, strip, swapcase, title, translate, upper, zfill
- `http.HTTPStatus(*values)` — ACCEPTED, ALREADY_REPORTED, BAD_GATEWAY, BAD_REQUEST, CONFLICT, CONTENT_TOO_LARGE, CONTINUE, CREATED, EARLY_HINTS, EXPECTATION_FAILED, FAILED_DEPENDENCY, FORBIDDEN, FOUND, GATEWAY_TIMEOUT, GONE, HTTP_VERSION_NOT_SUPPORTED, IM_A_TEAPOT, IM_USED, INSUFFICIENT_STORAGE, INTERNAL_SERVER_ERROR, LENGTH_REQUIRED, LOCKED, LOOP_DETECTED, METHOD_NOT_ALLOWED, MISDIRECTED_REQUEST, MOVED_PERMANENTLY, MULTIPLE_CHOICES, MULTI_STATUS, NETWORK_AUTHENTICATION_REQUIRED, NON_AUTHORITATIVE_INFORMATION, NOT_ACCEPTABLE, NOT_EXTENDED, NOT_FOUND, NOT_IMPLEMENTED, NOT_MODIFIED, NO_CONTENT, OK, PARTIAL_CONTENT, PAYMENT_REQUIRED, PERMANENT_REDIRECT, PRECONDITION_FAILED, PRECONDITION_REQUIRED, PROCESSING, PROXY_AUTHENTICATION_REQUIRED, RANGE_NOT_SATISFIABLE, REQUEST_HEADER_FIELDS_TOO_LARGE, REQUEST_TIMEOUT, RESET_CONTENT, SEE_OTHER, SERVICE_UNAVAILABLE, SWITCHING_PROTOCOLS, TEMPORARY_REDIRECT, TOO_EARLY, TOO_MANY_REQUESTS, UNAUTHORIZED, UNAVAILABLE_FOR_LEGAL_REASONS, UNPROCESSABLE_CONTENT, UNSUPPORTED_MEDIA_TYPE, UPGRADE_REQUIRED, URI_TOO_LONG, USE_PROXY, VARIANT_ALSO_NEGOTIATES, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes

### http.client

_подмодуль · импорт 385 мс_

> HTTP/1.1 client library

Классы (3):

- `http.client.HTTPConnection(host, port=None, timeout=<object object>, source_address=None, blocksize=8192)` — auto_open, close, connect, debuglevel, default_port, endheaders, get_proxy_response_headers, getresponse, putheader, putrequest, request, response_class, send, set_debuglevel, set_tunnel
- `http.client.HTTPResponse(sock, debuglevel=0, method=None, url=None)` — begin, close, closed, detach, fileno, flush, getcode, getheader, getheaders, geturl, info, isatty, isclosed, peek, read, read1, readable, readinto, readinto1, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `http.client.HTTPSConnection(host, port=None, *, timeout=<object object>, source_address=None, context=None, blocksize=8192)` — auto_open, close, connect, debuglevel, default_port, endheaders, get_proxy_response_headers, getresponse, putheader, putrequest, request, response_class, send, set_debuglevel, set_tunnel

Исключения: `http.client.BadStatusLine`, `http.client.CannotSendHeader`, `http.client.CannotSendRequest`, `http.client.HTTPException`, `http.client.ImproperConnectionState`, `http.client.IncompleteRead`, `http.client.InvalidURL`, `http.client.LineTooLong`, `http.client.NotConnected`, `http.client.RemoteDisconnected`, `http.client.ResponseNotReady`, `http.client.UnimplementedFileMode`, `http.client.UnknownProtocol`, `http.client.UnknownTransferEncoding`, `http.client.error`.

Константы и данные: `http.client.responses` (dict).

### http.cookiejar

_подмодуль · импорт 632 мс_

> HTTP cookie handling for web clients.

Классы (7):

- `http.cookiejar.Cookie(version, name, value, port, port_specified, domain, domain_specified, domain_initial_dot, path, path_specified, secure, expires, discard, comment, comment_url, rest, rfc2109=False)` — get_nonstandard_attr, has_nonstandard_attr, is_expired, set_nonstandard_attr
- `http.cookiejar.CookieJar(policy=None)` — add_cookie_header, clear, clear_expired_cookies, clear_session_cookies, domain_re, dots_re, extract_cookies, magic_re, make_cookies, non_word_re, quote_re, set_cookie, set_cookie_if_ok, set_policy, strict_domain_re
- `http.cookiejar.CookiePolicy()` — domain_return_ok, path_return_ok, return_ok, set_ok
- `http.cookiejar.DefaultCookiePolicy(blocked_domains=None, allowed_domains=None, netscape=True, rfc2965=False, rfc2109_as_netscape=None, hide_cookie2=False, strict_domain=False, strict_rfc2965_unverifiable=True, strict_ns_unverifiable=False, strict_ns_domain=0, strict_ns_set…` — DomainLiberal, DomainRFC2965Match, DomainStrict, DomainStrictNoDots, DomainStrictNonDomain, allowed_domains, blocked_domains, domain_return_ok, is_blocked, is_not_allowed, path_return_ok, return_ok, return_ok_domain, return_ok_expires, return_ok_port, return_ok_secure, return_ok_verifiability, return_ok_version, set_allowed_domains, set_blocked_domains, set_ok, set_ok_domain, set_ok_name, set_ok_path, set_ok_port, set_ok_verifiability, set_ok_version
- `http.cookiejar.FileCookieJar(filename=None, delayload=False, policy=None)` — add_cookie_header, clear, clear_expired_cookies, clear_session_cookies, domain_re, dots_re, extract_cookies, load, magic_re, make_cookies, non_word_re, quote_re, revert, save, set_cookie, set_cookie_if_ok, set_policy, strict_domain_re
- `http.cookiejar.LWPCookieJar(filename=None, delayload=False, policy=None)` — add_cookie_header, as_lwp_str, clear, clear_expired_cookies, clear_session_cookies, domain_re, dots_re, extract_cookies, load, magic_re, make_cookies, non_word_re, quote_re, revert, save, set_cookie, set_cookie_if_ok, set_policy, strict_domain_re
- `http.cookiejar.MozillaCookieJar(filename=None, delayload=False, policy=None)` — add_cookie_header, clear, clear_expired_cookies, clear_session_cookies, domain_re, dots_re, extract_cookies, load, magic_re, make_cookies, non_word_re, quote_re, revert, save, set_cookie, set_cookie_if_ok, set_policy, strict_domain_re

Исключения: `http.cookiejar.LoadError`.

### http.cookies

_подмодуль · импорт 52 мс_

> Here's a sample session to show how to use this module.

Классы (2):

- `http.cookies.BaseCookie(input=None)` — clear, copy, fromkeys, get, items, js_output, keys, load, output, pop, popitem, setdefault, update, value_decode, value_encode, values
- `http.cookies.SimpleCookie(input=None)` — clear, copy, fromkeys, get, items, js_output, keys, load, output, pop, popitem, setdefault, update, value_decode, value_encode, values

Исключения: `http.cookies.CookieError`.

### http.server

_подмодуль · импорт 523 мс_

> HTTP server classes.

Классы (5):

- `http.server.BaseHTTPRequestHandler(request, client_address, server)` — MessageClass, address_string, date_time_string, default_request_version, disable_nagle_algorithm, end_headers, error_content_type, error_message_format, finish, flush_headers, handle, handle_expect_100, handle_one_request, log_date_time_string, log_error, log_message, log_request, monthname, parse_request, protocol_version, rbufsize, responses, send_error, send_header, send_response, send_response_only, server_version, setup, sys_version, timeout, version_string, wbufsize, weekdayname
- `http.server.CGIHTTPRequestHandler(*args, **kwargs)` — MessageClass, address_string, cgi_directories, copyfile, date_time_string, default_request_version, disable_nagle_algorithm, do_GET, do_HEAD, do_POST, end_headers, error_content_type, error_message_format, extensions_map, finish, flush_headers, guess_type, handle, handle_expect_100, handle_one_request, have_fork, index_pages, is_cgi, is_executable, is_python, list_directory, log_date_time_string, log_error, log_message, log_request, monthname, parse_request, protocol_version, rbufsize, responses, run_cgi, send_error, send_head, send_header, send_response, send_response_only, server_version, setup, sys_version, timeout, translate_path, version_string, wbufsize, weekdayname
- `http.server.HTTPServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, close_request, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, process_request, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `http.server.SimpleHTTPRequestHandler(*args, directory=None, **kwargs)` — MessageClass, address_string, copyfile, date_time_string, default_request_version, disable_nagle_algorithm, do_GET, do_HEAD, end_headers, error_content_type, error_message_format, extensions_map, finish, flush_headers, guess_type, handle, handle_expect_100, handle_one_request, index_pages, list_directory, log_date_time_string, log_error, log_message, log_request, monthname, parse_request, protocol_version, rbufsize, responses, send_error, send_head, send_header, send_response, send_response_only, server_version, setup, sys_version, timeout, translate_path, version_string, wbufsize, weekdayname
- `http.server.ThreadingHTTPServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, block_on_close, close_request, daemon_threads, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, process_request, process_request_thread, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request

### imaplib

_Python · импорт 316 мс_

> IMAP4 client.

Функции (4):

- `imaplib.Int2AP(num)`
- `imaplib.Internaldate2tuple(resp)`
- `imaplib.ParseFlags(resp)`
- `imaplib.Time2Internaldate(date_time)`

Классы (3):

- `imaplib.IMAP4(host='', port=143, timeout=None)` — abort, append, authenticate, capability, check, close, copy, create, delete, deleteacl, enable, error, expunge, fetch, getacl, getannotation, getquota, getquotaroot, list, login, login_cram_md5, logout, lsub, myrights, namespace, noop, open, partial, print_log, proxyauth, read, readline, readonly, recent, rename, response, search, select, send, setacl, setannotation, setquota, shutdown, socket, sort, starttls, status, store, subscribe, thread, uid, unselect, unsubscribe, xatom
- `imaplib.IMAP4_SSL(host='', port=993, *, ssl_context=None, timeout=None)` — abort, append, authenticate, capability, check, close, copy, create, delete, deleteacl, enable, error, expunge, fetch, getacl, getannotation, getquota, getquotaroot, list, login, login_cram_md5, logout, lsub, myrights, namespace, noop, open, partial, print_log, proxyauth, read, readline, readonly, recent, rename, response, search, select, send, setacl, setannotation, setquota, shutdown, socket, sort, starttls, status, store, subscribe, thread, uid, unselect, unsubscribe, xatom
- `imaplib.IMAP4_stream(command)` — abort, append, authenticate, capability, check, close, copy, create, delete, deleteacl, enable, error, expunge, fetch, getacl, getannotation, getquota, getquotaroot, list, login, login_cram_md5, logout, lsub, myrights, namespace, noop, open, partial, print_log, proxyauth, read, readline, readonly, recent, rename, response, search, select, send, setacl, setannotation, setquota, shutdown, socket, sort, starttls, status, store, subscribe, thread, uid, unselect, unsubscribe, xatom

### io

_Python · импорт 0.02 мс_

> The io module provides the Python interfaces to stream handling. The

Функции (3):

- `io.open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)`
- `io.open_code(path)`
- `io.text_encoding(encoding, stacklevel=2, /)`

Классы (13):

- `io.BufferedIOBase()` — close, closed, detach, fileno, flush, isatty, read, read1, readable, readinto, readinto1, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `io.BufferedRWPair(reader, writer, buffer_size=8192, /)` — close, closed, detach, fileno, flush, isatty, peek, read, read1, readable, readinto, readinto1, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `io.BufferedRandom(raw, buffer_size=8192)` — close, closed, detach, fileno, flush, isatty, mode, name, peek, raw, read, read1, readable, readinto, readinto1, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `io.BufferedReader(raw, buffer_size=8192)` — close, closed, detach, fileno, flush, isatty, mode, name, peek, raw, read, read1, readable, readinto, readinto1, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `io.BufferedWriter(raw, buffer_size=8192)` — close, closed, detach, fileno, flush, isatty, mode, name, raw, read, read1, readable, readinto, readinto1, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `io.BytesIO(initial_bytes=b'')` — close, closed, detach, fileno, flush, getbuffer, getvalue, isatty, read, read1, readable, readinto, readinto1, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `io.FileIO(file, mode='r', closefd=True, opener=None)` — close, closed, closefd, fileno, flush, isatty, mode, read, readable, readall, readinto, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `io.IOBase()` — close, closed, fileno, flush, isatty, readable, readline, readlines, seek, seekable, tell, truncate, writable, writelines
- `io.IncrementalNewlineDecoder(decoder, translate, errors='strict')` — decode, getstate, newlines, reset, setstate
- `io.RawIOBase()` — close, closed, fileno, flush, isatty, read, readable, readall, readinto, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `io.StringIO(initial_value='', newline='\n')` — close, closed, detach, encoding, errors, fileno, flush, getvalue, isatty, line_buffering, newlines, read, readable, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `io.TextIOBase()` — close, closed, detach, encoding, errors, fileno, flush, isatty, newlines, read, readable, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `io.TextIOWrapper(buffer, encoding=None, errors=None, newline=None, line_buffering=False, write_through=False)` — buffer, close, closed, detach, encoding, errors, fileno, flush, isatty, line_buffering, name, newlines, read, readable, readline, readlines, reconfigure, seek, seekable, tell, truncate, writable, write, write_through, writelines

Исключения: `io.BlockingIOError`, `io.UnsupportedOperation`.

Константы и данные: `io.DEFAULT_BUFFER_SIZE` = `8192`, `io.SEEK_CUR` = `1`, `io.SEEK_END` = `2`, `io.SEEK_SET` = `0`.

### ipaddress

_Python · импорт 25 мс_

> A fast, lightweight IPv4/IPv6 manipulation library in Python.

Функции (8):

- `ipaddress.collapse_addresses(addresses)`
- `ipaddress.get_mixed_type_key(obj)`
- `ipaddress.ip_address(address)`
- `ipaddress.ip_interface(address)`
- `ipaddress.ip_network(address, strict=True)`
- `ipaddress.summarize_address_range(first, last)`
- `ipaddress.v4_int_to_packed(address)`
- `ipaddress.v6_int_to_packed(address)`

Классы (6):

- `ipaddress.IPv4Address(address)` — compressed, exploded, ipv6_mapped, is_global, is_link_local, is_loopback, is_multicast, is_private, is_reserved, is_unspecified, max_prefixlen, packed, reverse_pointer, version
- `ipaddress.IPv4Interface(address)` — compressed, exploded, hostmask, ip, ipv6_mapped, is_global, is_link_local, is_loopback, is_multicast, is_private, is_reserved, is_unspecified, max_prefixlen, packed, reverse_pointer, version, with_hostmask, with_netmask, with_prefixlen
- `ipaddress.IPv4Network(address, strict=True)` — address_exclude, broadcast_address, compare_networks, compressed, exploded, hostmask, hosts, is_global, is_link_local, is_loopback, is_multicast, is_private, is_reserved, is_unspecified, max_prefixlen, num_addresses, overlaps, prefixlen, reverse_pointer, subnet_of, subnets, supernet, supernet_of, version, with_hostmask, with_netmask, with_prefixlen
- `ipaddress.IPv6Address(address)` — compressed, exploded, ipv4_mapped, is_global, is_link_local, is_loopback, is_multicast, is_private, is_reserved, is_site_local, is_unspecified, max_prefixlen, packed, reverse_pointer, scope_id, sixtofour, teredo, version
- `ipaddress.IPv6Interface(address)` — compressed, exploded, hostmask, ip, ipv4_mapped, is_global, is_link_local, is_loopback, is_multicast, is_private, is_reserved, is_site_local, is_unspecified, max_prefixlen, packed, reverse_pointer, scope_id, sixtofour, teredo, version, with_hostmask, with_netmask, with_prefixlen
- `ipaddress.IPv6Network(address, strict=True)` — address_exclude, broadcast_address, compare_networks, compressed, exploded, hostmask, hosts, is_global, is_link_local, is_loopback, is_multicast, is_private, is_reserved, is_site_local, is_unspecified, max_prefixlen, num_addresses, overlaps, prefixlen, reverse_pointer, subnet_of, subnets, supernet, supernet_of, version, with_hostmask, with_netmask, with_prefixlen

Исключения: `ipaddress.AddressValueError`, `ipaddress.NetmaskValueError`.

Константы и данные: `ipaddress.IPV4LENGTH` = `32`, `ipaddress.IPV6LENGTH` = `128`.

### itertools

_встроенный · импорт 0.02 мс_

> Functional tools for creating and using iterators.

Функции (1):

- `itertools.tee(iterable, n=2, /)`

Классы (19):

- `itertools.accumulate(iterable, func=None, *, initial=None)`
- `itertools.batched(iterable, n, *, strict=False)`
- `itertools.chain(*iterables)` — from_iterable
- `itertools.combinations(iterable, r)`
- `itertools.combinations_with_replacement(iterable, r)`
- `itertools.compress(data, selectors)`
- `itertools.count(start=0, step=1)`
- `itertools.cycle(iterable, /)`
- `itertools.dropwhile(predicate, iterable, /)`
- `itertools.filterfalse(function, iterable, /)`
- `itertools.groupby(iterable, key=None)`
- `itertools.islice(iterable, stop) --> islice object`
- `itertools.pairwise(iterable, /)`
- `itertools.permutations(iterable, r=None)`
- `itertools.product(*iterables, repeat=1)`
- `itertools.repeat(object [,times]) -> create an iterator which returns the object`
- `itertools.starmap(function, iterable, /)`
- `itertools.takewhile(predicate, iterable, /)`
- `itertools.zip_longest(*iterables, fillvalue=None)`

### json

_пакет · импорт 44 мс_

> JSON (JavaScript Object Notation) <https://json.org> is a subset of

Подмодули: `json.decoder`, `json.encoder`, `json.scanner`, `json.tool`.

Функции (4):

- `json.dump(obj, fp, *, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)`
- `json.dumps(obj, *, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)`
- `json.load(fp, *, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)`
- `json.loads(s, *, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)`

Классы (2):

- `json.JSONDecoder(*, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, strict=True, object_pairs_hook=None)` — decode, raw_decode
- `json.JSONEncoder(*, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, sort_keys=False, indent=None, separators=None, default=None)` — default, encode, item_separator, iterencode, key_separator

Исключения: `json.JSONDecodeError`.

### json.decoder

_подмодуль · импорт 33 мс_

> Implementation of JSONDecoder

Классы (1):

- `json.decoder.JSONDecoder(*, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, strict=True, object_pairs_hook=None)` — decode, raw_decode

Исключения: `json.decoder.JSONDecodeError`.

### json.encoder

_подмодуль · импорт 34 мс_

> Implementation of JSONEncoder

Функции (6):

- `json.encoder.c_encode_basestring(object, /)`
- `json.encoder.c_encode_basestring_ascii(object, /)`
- `json.encoder.encode_basestring(object, /)`
- `json.encoder.encode_basestring_ascii(object, /)`
- `json.encoder.py_encode_basestring(s)`
- `json.encoder.py_encode_basestring_ascii(s)`

Классы (2):

- `json.encoder.JSONEncoder(*, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, sort_keys=False, indent=None, separators=None, default=None)` — default, encode, item_separator, iterencode, key_separator
- `json.encoder.c_make_encoder(markers, default, encoder, indent, key_separator, item_separator, sort_keys, skipkeys, allow_nan)` — default, encoder, indent, item_separator, key_separator, markers, skipkeys, sort_keys

Константы и данные: `json.encoder.ESCAPE` (Pattern), `json.encoder.ESCAPE_ASCII` (Pattern), `json.encoder.ESCAPE_DCT` (dict), `json.encoder.HAS_UTF8` (Pattern), `json.encoder.INFINITY` = `inf`.

### json.scanner

_подмодуль · импорт 34 мс_

> JSON token scanner

Классы (1):

- `json.scanner.make_scanner(…)` — object_hook, object_pairs_hook, parse_constant, parse_float, parse_int, strict

### json.tool

_подмодуль · импорт 75 мс_

> Command-line tool to validate and pretty-print JSON

Функции (1):

- `json.tool.main()`

### keyword

_Python · импорт 0.02 мс_

> Keywords (from "Grammar/python.gram")

Функции (2):

- `keyword.iskeyword(object, /)`
- `keyword.issoftkeyword(object, /)`

Константы и данные: `keyword.kwlist` (list), `keyword.softkwlist` (list).

### locale

_Python · импорт 18 мс_

> Locale support module.

Функции (14):

- `locale.atof(string, func=<class 'float'>)`
- `locale.atoi(string)`
- `locale.currency(val, symbol=True, grouping=False, international=False)`
- `locale.format_string(f, val, grouping=False, monetary=False)`
- `locale.getdefaultlocale(envvars=('LC_ALL', 'LC_CTYPE', 'LANG', 'LANGUAGE'))`
- `locale.getencoding()`
- `locale.getlocale(category=0)`
- `locale.getpreferredencoding(do_setlocale=True)`
- `locale.localeconv()`
- `locale.normalize(localename)`
- `locale.setlocale(category, locale=None)`
- `locale.str(val)`
- `locale.strcoll(os1, os2, /)`
- `locale.strxfrm(string, /)`

Исключения: `locale.Error`.

Константы и данные: `locale.CHAR_MAX` = `255`, `locale.LC_ALL` = `6`, `locale.LC_COLLATE` = `3`, `locale.LC_CTYPE` = `0`, `locale.LC_MESSAGES` = `5`, `locale.LC_MONETARY` = `4`, `locale.LC_NUMERIC` = `1`, `locale.LC_TIME` = `2`.

### mailbox

_Python · импорт 356 мс_

> Read/write support for Maildir, mbox, MH, Babyl, and MMDF mailboxes.

Классы (12):

- `mailbox.Babyl(path, factory=None, create=True)` — add, clear, close, discard, flush, get, get_bytes, get_file, get_labels, get_message, get_string, items, iteritems, iterkeys, itervalues, keys, lock, pop, popitem, remove, unlock, update, values
- `mailbox.BabylMessage(message=None)` — add_header, add_label, as_bytes, as_string, attach, del_param, get, get_all, get_boundary, get_charset, get_charsets, get_content_charset, get_content_disposition, get_content_maintype, get_content_subtype, get_content_type, get_default_type, get_filename, get_labels, get_param, get_params, get_payload, get_unixfrom, get_visible, is_multipart, items, keys, raw_items, remove_label, replace_header, set_boundary, set_charset, set_default_type, set_labels, set_param, set_payload, set_raw, set_type, set_unixfrom, set_visible, update_visible, values, walk
- `mailbox.MH(path, factory=None, create=True)` — add, add_folder, clear, close, discard, flush, get, get_bytes, get_file, get_folder, get_message, get_sequences, get_string, items, iteritems, iterkeys, itervalues, keys, list_folders, lock, pack, pop, popitem, remove, remove_folder, set_sequences, unlock, update, values
- `mailbox.MHMessage(message=None)` — add_header, add_sequence, as_bytes, as_string, attach, del_param, get, get_all, get_boundary, get_charset, get_charsets, get_content_charset, get_content_disposition, get_content_maintype, get_content_subtype, get_content_type, get_default_type, get_filename, get_param, get_params, get_payload, get_sequences, get_unixfrom, is_multipart, items, keys, raw_items, remove_sequence, replace_header, set_boundary, set_charset, set_default_type, set_param, set_payload, set_raw, set_sequences, set_type, set_unixfrom, values, walk
- `mailbox.MMDF(path, factory=None, create=True)` — add, clear, close, discard, flush, get, get_bytes, get_file, get_message, get_string, items, iteritems, iterkeys, itervalues, keys, lock, pop, popitem, remove, unlock, update, values
- `mailbox.MMDFMessage(message=None)` — add_flag, add_header, as_bytes, as_string, attach, del_param, get, get_all, get_boundary, get_charset, get_charsets, get_content_charset, get_content_disposition, get_content_maintype, get_content_subtype, get_content_type, get_default_type, get_filename, get_flags, get_from, get_param, get_params, get_payload, get_unixfrom, is_multipart, items, keys, raw_items, remove_flag, replace_header, set_boundary, set_charset, set_default_type, set_flags, set_from, set_param, set_payload, set_raw, set_type, set_unixfrom, values, walk
- `mailbox.Mailbox(path, factory=None, create=True)` — add, clear, close, discard, flush, get, get_bytes, get_file, get_message, get_string, items, iteritems, iterkeys, itervalues, keys, lock, pop, popitem, remove, unlock, update, values
- `mailbox.Maildir(dirname, factory=None, create=True)` — add, add_flag, add_folder, clean, clear, close, colon, discard, flush, get, get_bytes, get_file, get_flags, get_folder, get_info, get_message, get_string, items, iteritems, iterkeys, itervalues, keys, list_folders, lock, next, pop, popitem, remove, remove_flag, remove_folder, set_flags, set_info, unlock, update, values
- `mailbox.MaildirMessage(message=None)` — add_flag, add_header, as_bytes, as_string, attach, del_param, get, get_all, get_boundary, get_charset, get_charsets, get_content_charset, get_content_disposition, get_content_maintype, get_content_subtype, get_content_type, get_date, get_default_type, get_filename, get_flags, get_info, get_param, get_params, get_payload, get_subdir, get_unixfrom, is_multipart, items, keys, raw_items, remove_flag, replace_header, set_boundary, set_charset, set_date, set_default_type, set_flags, set_info, set_param, set_payload, set_raw, set_subdir, set_type, set_unixfrom, values, walk
- `mailbox.Message(message=None)` — add_header, as_bytes, as_string, attach, del_param, get, get_all, get_boundary, get_charset, get_charsets, get_content_charset, get_content_disposition, get_content_maintype, get_content_subtype, get_content_type, get_default_type, get_filename, get_param, get_params, get_payload, get_unixfrom, is_multipart, items, keys, raw_items, replace_header, set_boundary, set_charset, set_default_type, set_param, set_payload, set_raw, set_type, set_unixfrom, values, walk
- `mailbox.mbox(path, factory=None, create=True)` — add, clear, close, discard, flush, get, get_bytes, get_file, get_message, get_string, items, iteritems, iterkeys, itervalues, keys, lock, pop, popitem, remove, unlock, update, values
- `mailbox.mboxMessage(message=None)` — add_flag, add_header, as_bytes, as_string, attach, del_param, get, get_all, get_boundary, get_charset, get_charsets, get_content_charset, get_content_disposition, get_content_maintype, get_content_subtype, get_content_type, get_default_type, get_filename, get_flags, get_from, get_param, get_params, get_payload, get_unixfrom, is_multipart, items, keys, raw_items, remove_flag, replace_header, set_boundary, set_charset, set_default_type, set_flags, set_from, set_param, set_payload, set_raw, set_type, set_unixfrom, values, walk

Исключения: `mailbox.Error`, `mailbox.ExternalClashError`, `mailbox.FormatError`, `mailbox.NoSuchMailboxError`, `mailbox.NotEmptyError`.

### math

_расширение (.so) · импорт 3.2 мс_

> This module provides access to the mathematical functions

Функции (57):

- `math.acos(x, /)`
- `math.acosh(x, /)`
- `math.asin(x, /)`
- `math.asinh(x, /)`
- `math.atan(x, /)`
- `math.atan2(y, x, /)`
- `math.atanh(x, /)`
- `math.cbrt(x, /)`
- `math.ceil(x, /)`
- `math.comb(n, k, /)`
- `math.copysign(x, y, /)`
- `math.cos(x, /)`
- `math.cosh(x, /)`
- `math.degrees(x, /)`
- `math.dist(p, q, /)`
- `math.erf(x, /)`
- `math.erfc(x, /)`
- `math.exp(x, /)`
- `math.exp2(x, /)`
- `math.expm1(x, /)`
- `math.fabs(x, /)`
- `math.factorial(n, /)`
- `math.floor(x, /)`
- `math.fma(x, y, z, /)`
- `math.fmod(x, y, /)`
- `math.frexp(x, /)`
- `math.fsum(seq, /)`
- `math.gamma(x, /)`
- `math.gcd(*integers)`
- `math.hypot(*coordinates) -> value`
- `math.isclose(a, b, *, rel_tol=1e-09, abs_tol=0.0)`
- `math.isfinite(x, /)`
- `math.isinf(x, /)`
- `math.isnan(x, /)`
- `math.isqrt(n, /)`
- `math.lcm(*integers)`
- `math.ldexp(x, i, /)`
- `math.lgamma(x, /)`
- `math.log(x, [base=math.e])`
- `math.log10(x, /)`
- `math.log1p(x, /)`
- `math.log2(x, /)`
- `math.modf(x, /)`
- `math.nextafter(x, y, /, *, steps=None)`
- `math.perm(n, k=None, /)`
- `math.pow(x, y, /)`
- `math.prod(iterable, /, *, start=1)`
- `math.radians(x, /)`
- `math.remainder(x, y, /)`
- `math.sin(x, /)`
- `math.sinh(x, /)`
- `math.sqrt(x, /)`
- `math.sumprod(p, q, /)`
- `math.tan(x, /)`
- `math.tanh(x, /)`
- `math.trunc(x, /)`
- `math.ulp(x, /)`

Константы и данные: `math.e` = `2.718281828459045`, `math.inf` = `inf`, `math.nan` = `nan`, `math.pi` = `3.141592653589793`, `math.tau` = `6.283185307179586`.

### numbers

_Python · импорт 7.2 мс_

> Abstract Base Classes (ABCs) for numbers, according to PEP 3141.

Классы (5):

- `numbers.Complex()` — conjugate, imag, real
- `numbers.Integral()` — conjugate, denominator, imag, numerator, real
- `numbers.Number()`
- `numbers.Rational()` — conjugate, denominator, imag, numerator, real
- `numbers.Real()` — conjugate, imag, real

### operator

_Python · импорт 0.02 мс_

> Operator interface.

Функции (52):

- `operator.abs(a, /)`
- `operator.add(a, b, /)`
- `operator.and_(a, b, /)`
- `operator.call(obj, /, *args, **kwargs)`
- `operator.concat(a, b, /)`
- `operator.contains(a, b, /)`
- `operator.countOf(a, b, /)`
- `operator.delitem(a, b, /)`
- `operator.eq(a, b, /)`
- `operator.floordiv(a, b, /)`
- `operator.ge(a, b, /)`
- `operator.getitem(a, b, /)`
- `operator.gt(a, b, /)`
- `operator.iadd(a, b, /)`
- `operator.iand(a, b, /)`
- `operator.iconcat(a, b, /)`
- `operator.ifloordiv(a, b, /)`
- `operator.ilshift(a, b, /)`
- `operator.imatmul(a, b, /)`
- `operator.imod(a, b, /)`
- `operator.imul(a, b, /)`
- `operator.index(a, /)`
- `operator.indexOf(a, b, /)`
- `operator.inv(a, /)`
- `operator.invert(a, /)`
- `operator.ior(a, b, /)`
- `operator.ipow(a, b, /)`
- `operator.irshift(a, b, /)`
- `operator.is_(a, b, /)`
- `operator.is_not(a, b, /)`
- `operator.isub(a, b, /)`
- `operator.itruediv(a, b, /)`
- `operator.ixor(a, b, /)`
- `operator.le(a, b, /)`
- `operator.length_hint(obj, default=0, /)`
- `operator.lshift(a, b, /)`
- `operator.lt(a, b, /)`
- `operator.matmul(a, b, /)`
- `operator.mod(a, b, /)`
- `operator.mul(a, b, /)`
- `operator.ne(a, b, /)`
- `operator.neg(a, /)`
- `operator.not_(a, /)`
- `operator.or_(a, b, /)`
- `operator.pos(a, /)`
- `operator.pow(a, b, /)`
- `operator.rshift(a, b, /)`
- `operator.setitem(a, b, c, /)`
- `operator.sub(a, b, /)`
- `operator.truediv(a, b, /)`
- `operator.truth(a, /)`
- `operator.xor(a, b, /)`

Классы (3):

- `operator.attrgetter(attr, /, *attrs)`
- `operator.itemgetter(item, /, *items)`
- `operator.methodcaller(name, /, *args, **kwargs)`

### os

_Python · импорт 0.02 мс_

> OS routines for NT or Posix depending on what system we're on.

Функции (197):

- `os.WCOREDUMP(status, /)`
- `os.WEXITSTATUS(status)`
- `os.WIFCONTINUED(status)`
- `os.WIFEXITED(status)`
- `os.WIFSIGNALED(status)`
- `os.WIFSTOPPED(status)`
- `os.WSTOPSIG(status)`
- `os.WTERMSIG(status)`
- `os._exit(status)`
- `os.abort()`
- `os.access(path, mode, *, dir_fd=None, effective_ids=False, follow_symlinks=True)`
- `os.chdir(path)`
- `os.chmod(…)`
- `os.chown(path, uid, gid, *, dir_fd=None, follow_symlinks=True)`
- `os.chroot(path)`
- `os.close(fd)`
- `os.closerange(fd_low, fd_high, /)`
- `os.confstr(name, /)`
- `os.cpu_count()`
- `os.ctermid()`
- `os.device_encoding(fd)`
- `os.dup(fd, /)`
- `os.dup2(fd, fd2, inheritable=True)`
- `os.eventfd(initval, flags=524288)`
- `os.eventfd_read(fd)`
- `os.eventfd_write(fd, value)`
- `os.execl(file, *args)`
- `os.execle(file, *args)`
- `os.execlp(file, *args)`
- `os.execlpe(file, *args)`
- `os.execv(path, argv, /)`
- `os.execve(path, argv, env)`
- `os.execvp(file, args)`
- `os.execvpe(file, args, env)`
- `os.fchdir(fd)`
- `os.fchmod(fd, mode)`
- `os.fchown(fd, uid, gid)`
- `os.fdatasync(fd)`
- `os.fdopen(fd, mode='r', buffering=-1, encoding=None, *args, **kwargs)`
- `os.fork()`
- `os.forkpty()`
- `os.fpathconf(fd, name, /)`
- `os.fsdecode(filename)`
- `os.fsencode(filename)`
- `os.fspath(path)`
- `os.fstat(fd)`
- `os.fstatvfs(fd, /)`
- `os.fsync(fd)`
- `os.ftruncate(fd, length, /)`
- `os.fwalk(top='.', topdown=True, onerror=None, *, follow_symlinks=False, dir_fd=None)`
- `os.get_blocking(fd, /)`
- `os.get_exec_path(env=None)`
- `os.get_inheritable(fd, /)`
- `os.get_terminal_size(…)`
- `os.getcwd()`
- `os.getcwdb()`
- `os.getegid()`
- `os.getenv(key, default=None)`
- `os.getenvb(key, default=None)`
- `os.geteuid()`
- `os.getgid()`
- `os.getgrouplist(user, group, /)`
- `os.getgroups()`
- `os.getloadavg()`
- `os.getlogin()`
- `os.getpgid(pid)`
- `os.getpgrp()`
- `os.getpid()`
- `os.getppid()`
- `os.getpriority(which, who)`
- `os.getrandom(size, flags=0)`
- `os.getresgid()`
- `os.getresuid()`
- `os.getsid(pid, /)`
- `os.getuid()`
- `os.getxattr(path, attribute, *, follow_symlinks=True)`
- `os.grantpt(fd, /)`
- `os.initgroups(username, gid, /)`
- `os.isatty(fd, /)`
- `os.kill(pid, signal, /)`
- `os.killpg(pgid, signal, /)`
- `os.lchown(path, uid, gid)`
- `os.link(src, dst, *, src_dir_fd=None, dst_dir_fd=None, follow_symlinks=True)`
- `os.listdir(path=None)`
- `os.listxattr(path=None, *, follow_symlinks=True)`
- `os.lockf(fd, command, length, /)`
- `os.login_tty(fd, /)`
- `os.lseek(fd, position, whence, /)`
- `os.lstat(path, *, dir_fd=None)`
- `os.major(device, /)`
- `os.makedev(major, minor, /)`
- `os.makedirs(name, mode=511, exist_ok=False)`
- `os.minor(device, /)`
- `os.mkdir(path, mode=511, *, dir_fd=None)`
- `os.mkfifo(path, mode=438, *, dir_fd=None)`
- `os.mknod(path, mode=384, device=0, *, dir_fd=None)`
- `os.nice(increment, /)`
- `os.open(path, flags, mode=511, *, dir_fd=None)`
- `os.openpty()`
- `os.pathconf(path, name)`
- `os.pipe()`
- `os.pipe2(flags, /)`
- `os.popen(cmd, mode='r', buffering=-1)`
- `os.posix_fadvise(fd, offset, length, advice, /)`
- `os.posix_fallocate(fd, offset, length, /)`
- `os.posix_openpt(oflag, /)`
- `os.posix_spawn(path, argv, env, /, *, file_actions=(), setpgroup=None, resetids=False, setsid=False, setsigmask=(), setsigdef=(), scheduler=None)`
- `os.posix_spawnp(path, argv, env, /, *, file_actions=(), setpgroup=None, resetids=False, setsid=False, setsigmask=(), setsigdef=(), scheduler=None)`
- `os.pread(fd, length, offset, /)`
- `os.preadv(fd, buffers, offset, flags=0, /)`
- `os.ptsname(fd, /)`
- `os.putenv(name, value, /)`
- `os.pwrite(fd, buffer, offset, /)`
- `os.pwritev(fd, buffers, offset, flags=0, /)`
- `os.read(fd, length, /)`
- `os.readlink(path, *, dir_fd=None)`
- `os.readv(fd, buffers, /)`
- `os.register_at_fork(…)`
- `os.remove(path, *, dir_fd=None)`
- `os.removedirs(name)`
- `os.removexattr(path, attribute, *, follow_symlinks=True)`
- `os.rename(src, dst, *, src_dir_fd=None, dst_dir_fd=None)`
- `os.renames(old, new)`
- `os.replace(src, dst, *, src_dir_fd=None, dst_dir_fd=None)`
- `os.rmdir(path, *, dir_fd=None)`
- `os.scandir(path=None)`
- `os.sched_get_priority_max(policy)`
- `os.sched_get_priority_min(policy)`
- `os.sched_getaffinity(pid, /)`
- `os.sched_getparam(pid, /)`
- `os.sched_getscheduler(pid, /)`
- `os.sched_rr_get_interval(pid, /)`
- `os.sched_setaffinity(pid, mask, /)`
- `os.sched_setparam(pid, param, /)`
- `os.sched_setscheduler(pid, policy, param, /)`
- `os.sched_yield()`
- `os.sendfile(out_fd, in_fd, offset, count)`
- `os.set_blocking(fd, blocking, /)`
- `os.set_inheritable(fd, inheritable, /)`
- `os.setegid(egid, /)`
- `os.seteuid(euid, /)`
- `os.setgid(gid, /)`
- `os.setgroups(groups, /)`
- `os.setns(fd, nstype=0)`
- `os.setpgid(pid, pgrp, /)`
- `os.setpgrp()`
- `os.setpriority(which, who, priority)`
- `os.setregid(rgid, egid, /)`
- `os.setresgid(rgid, egid, sgid, /)`
- `os.setresuid(ruid, euid, suid, /)`
- `os.setreuid(ruid, euid, /)`
- `os.setsid()`
- `os.setuid(uid, /)`
- `os.setxattr(path, attribute, value, flags=0, *, follow_symlinks=True)`
- `os.spawnl(mode, file, *args)`
- `os.spawnle(mode, file, *args)`
- `os.spawnlp(mode, file, *args)`
- `os.spawnlpe(mode, file, *args)`
- `os.spawnv(mode, file, args)`
- `os.spawnve(mode, file, args, env)`
- `os.spawnvp(mode, file, args)`
- `os.spawnvpe(mode, file, args, env)`
- `os.splice(src, dst, count, offset_src=None, offset_dst=None, flags=0)`
- `os.stat(path, *, dir_fd=None, follow_symlinks=True)`
- `os.statvfs(path)`
- `os.strerror(code, /)`
- `os.symlink(src, dst, target_is_directory=False, *, dir_fd=None)`
- `os.sync()`
- `os.sysconf(name, /)`
- `os.system(command)`
- `os.tcgetpgrp(fd, /)`
- `os.tcsetpgrp(fd, pgid, /)`
- `os.timerfd_create(clockid, /, *, flags=0)`
- `os.timerfd_gettime(fd, /)`
- `os.timerfd_gettime_ns(fd, /)`
- `os.timerfd_settime(fd, /, *, flags=0, initial=0.0, interval=0.0)`
- `os.timerfd_settime_ns(fd, /, *, flags=0, initial=0, interval=0)`
- `os.times()`
- `os.truncate(path, length)`
- `os.ttyname(fd, /)`
- `os.umask(mask, /)`
- `os.uname()`
- `os.unlink(path, *, dir_fd=None)`
- `os.unlockpt(fd, /)`
- `os.unsetenv(name, /)`
- `os.unshare(flags)`
- `os.urandom(size, /)`
- `os.utime(…)`
- `os.wait()`
- `os.wait3(options)`
- `os.wait4(pid, options)`
- `os.waitid(idtype, id, options, /)`
- `os.waitpid(pid, options, /)`
- `os.waitstatus_to_exitcode(status)`
- `os.walk(top, topdown=True, onerror=None, followlinks=False)`
- `os.write(fd, data, /)`
- `os.writev(fd, buffers, /)`

Классы (8):

- `os.DirEntry()` — inode, is_dir, is_file, is_junction, is_symlink, name, path, stat
- `os.sched_param(sched_priority)` — count, index, n_fields, n_sequence_fields, n_unnamed_fields, sched_priority
- `os.stat_result(iterable=(), /)` — count, index, n_fields, n_sequence_fields, n_unnamed_fields, st_atime, st_atime_ns, st_blksize, st_blocks, st_ctime, st_ctime_ns, st_dev, st_gid, st_ino, st_mode, st_mtime, st_mtime_ns, st_nlink, st_rdev, st_size, st_uid
- `os.statvfs_result(iterable=(), /)` — count, f_bavail, f_bfree, f_blocks, f_bsize, f_favail, f_ffree, f_files, f_flag, f_frsize, f_fsid, f_namemax, index, n_fields, n_sequence_fields, n_unnamed_fields
- `os.terminal_size(iterable=(), /)` — columns, count, index, lines, n_fields, n_sequence_fields, n_unnamed_fields
- `os.times_result(iterable=(), /)` — children_system, children_user, count, elapsed, index, n_fields, n_sequence_fields, n_unnamed_fields, system, user
- `os.uname_result(iterable=(), /)` — count, index, machine, n_fields, n_sequence_fields, n_unnamed_fields, nodename, release, sysname, version
- `os.waitid_result(iterable=(), /)` — count, index, n_fields, n_sequence_fields, n_unnamed_fields, si_code, si_pid, si_signo, si_status, si_uid

Исключения: `os.error`.

Константы и данные (152): `CLD_CONTINUED`, `CLD_DUMPED`, `CLD_EXITED`, `CLD_KILLED`, `CLD_STOPPED`, `CLD_TRAPPED`, `CLONE_FILES`, `CLONE_FS`, `CLONE_NEWCGROUP`, `CLONE_NEWIPC`, `CLONE_NEWNET`, `CLONE_NEWNS`, `CLONE_NEWPID`, `CLONE_NEWUSER`, `CLONE_NEWUTS`, `CLONE_SIGHAND`, `CLONE_SYSVSEM`, `CLONE_THREAD`, `CLONE_VM`, `EFD_CLOEXEC`, `EFD_NONBLOCK`, `EFD_SEMAPHORE`, `EX_CANTCREAT`, `EX_CONFIG`, `EX_DATAERR`, `EX_IOERR`, `EX_NOHOST`, `EX_NOINPUT`, `EX_NOPERM`, `EX_NOUSER`, `EX_OK`, `EX_OSERR`, `EX_OSFILE`, `EX_PROTOCOL`, `EX_SOFTWARE`, `EX_TEMPFAIL`, `EX_UNAVAILABLE`, `EX_USAGE`, `F_LOCK`, `F_OK`, `F_TEST`, `F_TLOCK`, `F_ULOCK`, `GRND_NONBLOCK`, `GRND_RANDOM`, `NGROUPS_MAX`, `O_ACCMODE`, `O_APPEND`, `O_ASYNC`, `O_CLOEXEC`, `O_CREAT`, `O_DIRECT`, `O_DIRECTORY`, `O_DSYNC`, `O_EXCL`, `O_FSYNC`, `O_LARGEFILE`, `O_NDELAY`, `O_NOATIME`, `O_NOCTTY`, `O_NOFOLLOW`, `O_NONBLOCK`, `O_PATH`, `O_RDONLY`, `O_RDWR`, `O_RSYNC`, `O_SYNC`, `O_TMPFILE`, `O_TRUNC`, `O_WRONLY`, `POSIX_FADV_DONTNEED`, `POSIX_FADV_NOREUSE`, `POSIX_FADV_NORMAL`, `POSIX_FADV_RANDOM`, `POSIX_FADV_SEQUENTIAL`, `POSIX_FADV_WILLNEED`, `POSIX_SPAWN_CLOSE`, `POSIX_SPAWN_DUP2`, `POSIX_SPAWN_OPEN`, `PRIO_PGRP`, `PRIO_PROCESS`, `PRIO_USER`, `P_ALL`, `P_NOWAIT`, `P_NOWAITO`, `P_PGID`, `P_PID`, `P_WAIT`, `RTLD_DEEPBIND`, `RTLD_GLOBAL`, `RTLD_LAZY`, `RTLD_LOCAL`, `RTLD_NODELETE`, `RTLD_NOLOAD`, `RTLD_NOW`, `R_OK`, `SCHED_BATCH`, `SCHED_FIFO`, `SCHED_IDLE`, `SCHED_OTHER`, `SCHED_RESET_ON_FORK`, `SCHED_RR`, `SEEK_CUR`, `SEEK_DATA`, `SEEK_END`, `SEEK_HOLE`, `SEEK_SET`, `SPLICE_F_MORE`, `SPLICE_F_MOVE`, `SPLICE_F_NONBLOCK`, `ST_APPEND`, `ST_MANDLOCK`, `ST_NOATIME`, `ST_NODEV`, `ST_NODIRATIME`, `ST_NOEXEC`, `ST_NOSUID`, `ST_RDONLY`, `ST_RELATIME`, `ST_SYNCHRONOUS`, `ST_WRITE`, `TFD_CLOEXEC`, `TFD_NONBLOCK`, `TFD_TIMER_ABSTIME`, `TMP_MAX`, `WCONTINUED`, `WEXITED`, `WNOHANG`, `WNOWAIT`, `WSTOPPED`, `WUNTRACED`, `W_OK`, `XATTR_CREATE`, `XATTR_REPLACE`, `XATTR_SIZE_MAX`, `X_OK`, `altsep`, `confstr_names`, `curdir`, `defpath`, `devnull`, `environ`, `environb`, `extsep`, `linesep`, `name`, `pardir`, `pathconf_names`, `pathsep`, `sep`, `supports_bytes_environ`, `sysconf_names`.

### pathlib

_пакет · импорт 53 мс_

> Object-oriented filesystem paths.

Классы (6):

- `pathlib.Path(*args, **kwargs)` — absolute, anchor, as_posix, as_uri, chmod, cwd, drive, exists, expanduser, from_uri, full_match, glob, group, hardlink_to, home, is_absolute, is_block_device, is_char_device, is_dir, is_fifo, is_file, is_junction, is_mount, is_relative_to, is_reserved, is_socket, is_symlink, iterdir, joinpath, lchmod, lstat, match, mkdir, name, open, owner, parent, parents, parser, parts, read_bytes, read_text, readlink, relative_to, rename, replace, resolve, rglob, rmdir, root, samefile, stat, stem, suffix, suffixes, symlink_to, touch, unlink, walk, with_name, with_segments, with_stem, with_suffix, write_bytes, write_text
- `pathlib.PosixPath(*args, **kwargs)` — absolute, anchor, as_posix, as_uri, chmod, cwd, drive, exists, expanduser, from_uri, full_match, glob, group, hardlink_to, home, is_absolute, is_block_device, is_char_device, is_dir, is_fifo, is_file, is_junction, is_mount, is_relative_to, is_reserved, is_socket, is_symlink, iterdir, joinpath, lchmod, lstat, match, mkdir, name, open, owner, parent, parents, parser, parts, read_bytes, read_text, readlink, relative_to, rename, replace, resolve, rglob, rmdir, root, samefile, stat, stem, suffix, suffixes, symlink_to, touch, unlink, walk, with_name, with_segments, with_stem, with_suffix, write_bytes, write_text
- `pathlib.PurePath(*args, **kwargs)` — anchor, as_posix, as_uri, drive, full_match, is_absolute, is_relative_to, is_reserved, joinpath, match, name, parent, parents, parser, parts, relative_to, root, stem, suffix, suffixes, with_name, with_segments, with_stem, with_suffix
- `pathlib.PurePosixPath(*args, **kwargs)` — anchor, as_posix, as_uri, drive, full_match, is_absolute, is_relative_to, is_reserved, joinpath, match, name, parent, parents, parser, parts, relative_to, root, stem, suffix, suffixes, with_name, with_segments, with_stem, with_suffix
- `pathlib.PureWindowsPath(*args, **kwargs)` — anchor, as_posix, as_uri, drive, full_match, is_absolute, is_relative_to, is_reserved, joinpath, match, name, parent, parents, parser, parts, relative_to, root, stem, suffix, suffixes, with_name, with_segments, with_stem, with_suffix
- `pathlib.WindowsPath(*args, **kwargs)` — absolute, anchor, as_posix, as_uri, chmod, cwd, drive, exists, expanduser, from_uri, full_match, glob, group, hardlink_to, home, is_absolute, is_block_device, is_char_device, is_dir, is_fifo, is_file, is_junction, is_mount, is_relative_to, is_reserved, is_socket, is_symlink, iterdir, joinpath, lchmod, lstat, match, mkdir, name, open, owner, parent, parents, parser, parts, read_bytes, read_text, readlink, relative_to, rename, replace, resolve, rglob, rmdir, root, samefile, stat, stem, suffix, suffixes, symlink_to, touch, unlink, walk, with_name, with_segments, with_stem, with_suffix, write_bytes, write_text

Исключения: `pathlib.UnsupportedOperation`.

### platform

_Python · импорт 18 мс_

> This module tries to retrieve as much platform-identifying data as

Функции (26):

- `platform.android_ver(release='', api_level=0, manufacturer='', model='', device='', is_emulator=False)`
- `platform.architecture(executable='/bin/python3', bits='', linkage='')`
- `platform.freedesktop_os_release()`
- `platform.ios_ver(system='', release='', model='', is_simulator=False)`
- `platform.java_ver(release='', vendor='', vminfo=('', '', ''), osinfo=('', '', ''))`
- `platform.libc_ver(executable=None, lib='', version='', chunksize=16384)`
- `platform.mac_ver(release='', versioninfo=('', '', ''), machine='')`
- `platform.machine()`
- `platform.node()`
- `platform.platform(aliased=False, terse=False)`
- `platform.processor()`
- `platform.python_branch()`
- `platform.python_build()`
- `platform.python_compiler()`
- `platform.python_implementation()`
- `platform.python_revision()`
- `platform.python_version()`
- `platform.python_version_tuple()`
- `platform.release()`
- `platform.system()`
- `platform.system_alias(system, release, version)`
- `platform.uname()`
- `platform.version()`
- `platform.win32_edition()`
- `platform.win32_is_iot()`
- `platform.win32_ver(release='', version='', csd='', ptype='')`

Классы (3):

- `platform.AndroidVer(release, api_level, manufacturer, model, device, is_emulator)` — api_level, count, device, index, is_emulator, manufacturer, model, release
- `platform.IOSVersionInfo(system, release, model, is_simulator)` — count, index, is_simulator, model, release, system
- `platform.uname_result(system, node, release, version, machine)` — count, index, machine, node, processor, release, system, version

### poplib

_Python · импорт 162 мс_

> A POP3 client class.

Классы (2):

- `poplib.POP3(host, port=110, timeout=<object object>)` — apop, capa, close, dele, encoding, getwelcome, list, noop, pass_, quit, retr, rpop, rset, set_debuglevel, stat, stls, timestamp, top, uidl, user, utf8
- `poplib.POP3_SSL(host, port=995, *, timeout=<object object>, context=None)` — apop, capa, close, dele, encoding, getwelcome, list, noop, pass_, quit, retr, rpop, rset, set_debuglevel, stat, stls, timestamp, top, uidl, user, utf8

Исключения: `poplib.error_proto`.

### posixpath

_Python · импорт 0.02 мс_

> Common operations on Posix pathnames.

Функции (32):

- `posixpath.abspath(path)`
- `posixpath.basename(p)`
- `posixpath.commonpath(paths)`
- `posixpath.commonprefix(m)`
- `posixpath.dirname(p)`
- `posixpath.exists(path)`
- `posixpath.expanduser(path)`
- `posixpath.expandvars(path)`
- `posixpath.getatime(filename)`
- `posixpath.getctime(filename)`
- `posixpath.getmtime(filename)`
- `posixpath.getsize(filename)`
- `posixpath.isabs(s)`
- `posixpath.isdevdrive(path)`
- `posixpath.isdir(s)`
- `posixpath.isfile(path)`
- `posixpath.isjunction(path)`
- `posixpath.islink(path)`
- `posixpath.ismount(path)`
- `posixpath.join(a, *p)`
- `posixpath.lexists(path)`
- `posixpath.normcase(s)`
- `posixpath.normpath(path)`
- `posixpath.realpath(filename, *, strict=False)`
- `posixpath.relpath(path, start=None)`
- `posixpath.samefile(f1, f2)`
- `posixpath.sameopenfile(fp1, fp2)`
- `posixpath.samestat(s1, s2)`
- `posixpath.split(p)`
- `posixpath.splitdrive(p)`
- `posixpath.splitext(p)`
- `posixpath.splitroot(p)`

Константы и данные: `posixpath.ALLOW_MISSING` (ALLOW_MISSING), `posixpath.altsep` (NoneType), `posixpath.curdir` = `'.'`, `posixpath.defpath` = `'/bin:/usr/bin'`, `posixpath.devnull` = `'/dev/null'`, `posixpath.extsep` = `'.'`, `posixpath.pardir` = `'..'`, `posixpath.pathsep` = `':'`, `posixpath.sep` = `'/'`, `posixpath.supports_unicode_filenames` = `False`.

### pprint

_Python · импорт 262 мс_

> Support to pretty-print lists, tuples, & dictionaries recursively.

Функции (6):

- `pprint.isreadable(object)`
- `pprint.isrecursive(object)`
- `pprint.pformat(object, indent=1, width=80, depth=None, *, compact=False, sort_dicts=True, underscore_numbers=False)`
- `pprint.pp(object, *args, sort_dicts=False, **kwargs)`
- `pprint.pprint(object, stream=None, indent=1, width=80, depth=None, *, compact=False, sort_dicts=True, underscore_numbers=False)`
- `pprint.saferepr(object)`

Классы (1):

- `pprint.PrettyPrinter(indent=1, width=80, depth=None, stream=None, *, compact=False, sort_dicts=True, underscore_numbers=False)` — format, isreadable, isrecursive, pformat, pprint

### quopri

_Python · импорт 7.0 мс_

> Conversions to/from quoted-printable transport encoding as per RFC 1521.

Функции (4):

- `quopri.decode(input, output, header=False)`
- `quopri.decodestring(s, header=False)`
- `quopri.encode(input, output, quotetabs, header=False)`
- `quopri.encodestring(s, quotetabs=False, header=False)`

### random

_Python · импорт 21 мс_

> Random variable generators.

Функции (24):

- `random.betavariate(alpha, beta)`
- `random.binomialvariate(n=1, p=0.5)`
- `random.choice(seq)`
- `random.choices(population, weights=None, *, cum_weights=None, k=1)`
- `random.expovariate(lambd=1.0)`
- `random.gammavariate(alpha, beta)`
- `random.gauss(mu=0.0, sigma=1.0)`
- `random.getrandbits(k, /)`
- `random.getstate()`
- `random.lognormvariate(mu, sigma)`
- `random.normalvariate(mu=0.0, sigma=1.0)`
- `random.paretovariate(alpha)`
- `random.randbytes(n)`
- `random.randint(a, b)`
- `random.random()`
- `random.randrange(start, stop=None, step=1)`
- `random.sample(population, k, *, counts=None)`
- `random.seed(a=None, version=2)`
- `random.setstate(state)`
- `random.shuffle(x)`
- `random.triangular(low=0.0, high=1.0, mode=None)`
- `random.uniform(a, b)`
- `random.vonmisesvariate(mu, kappa)`
- `random.weibullvariate(alpha, beta)`

Классы (2):

- `random.Random(x=None)` — VERSION, betavariate, binomialvariate, choice, choices, expovariate, gammavariate, gauss, getrandbits, getstate, lognormvariate, normalvariate, paretovariate, randbytes, randint, random, randrange, sample, seed, setstate, shuffle, triangular, uniform, vonmisesvariate, weibullvariate
- `random.SystemRandom(x=None)` — VERSION, betavariate, binomialvariate, choice, choices, expovariate, gammavariate, gauss, getrandbits, getstate, lognormvariate, normalvariate, paretovariate, randbytes, randint, random, randrange, sample, seed, setstate, shuffle, triangular, uniform, vonmisesvariate, weibullvariate

### re

_пакет · импорт 0.02 мс_

> Support for regular expressions (RE).

Функции (11):

- `re.compile(pattern, flags=0)`
- `re.escape(pattern)`
- `re.findall(pattern, string, flags=0)`
- `re.finditer(pattern, string, flags=0)`
- `re.fullmatch(pattern, string, flags=0)`
- `re.match(pattern, string, flags=0)`
- `re.purge()`
- `re.search(pattern, string, flags=0)`
- `re.split(pattern, string, maxsplit=0, flags=0)`
- `re.sub(pattern, repl, string, count=0, flags=0)`
- `re.subn(pattern, repl, string, count=0, flags=0)`

Классы (3):

- `re.Match()` — end, endpos, expand, group, groupdict, groups, lastgroup, lastindex, pos, re, regs, span, start, string
- `re.Pattern()` — findall, finditer, flags, fullmatch, groupindex, groups, match, pattern, scanner, search, split, sub, subn
- `re.RegexFlag(*values)` — ASCII, DEBUG, DOTALL, IGNORECASE, LOCALE, MULTILINE, UNICODE, VERBOSE, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes

Исключения: `re.PatternError`, `re.error`.

Константы и данные: `re.A` = `re.ASCII`, `re.ASCII` = `re.ASCII`, `re.DOTALL` = `re.DOTALL`, `re.I` = `re.IGNORECASE`, `re.IGNORECASE` = `re.IGNORECASE`, `re.L` = `re.LOCALE`, `re.LOCALE` = `re.LOCALE`, `re.M` = `re.MULTILINE`, `re.MULTILINE` = `re.MULTILINE`, `re.NOFLAG` = `re.NOFLAG`, `re.S` = `re.DOTALL`, `re.U` = `re.UNICODE`, `re.UNICODE` = `re.UNICODE`, `re.VERBOSE` = `re.VERBOSE`, `re.X` = `re.VERBOSE`.

### reprlib

_Python · импорт 0.02 мс_

> Redo the builtin repr() (representation) but with limits on most sizes.

Функции (2):

- `reprlib.recursive_repr(fillvalue='...')`
- `reprlib.repr(x)`

Классы (1):

- `reprlib.Repr(*, maxlevel=6, maxtuple=6, maxlist=6, maxarray=5, maxdict=4, maxset=6, maxfrozenset=6, maxdeque=6, maxstring=30, maxlong=40, maxother=30, fillvalue='...', indent=None)` — repr, repr1, repr_array, repr_deque, repr_dict, repr_frozenset, repr_instance, repr_int, repr_list, repr_set, repr_str, repr_tuple

### resource

_расширение (.so) · импорт 0.02 мс_

Функции (5):

- `resource.getpagesize()`
- `resource.getrlimit(resource, /)`
- `resource.getrusage(who, /)`
- `resource.prlimit(pid, resource, limits=None, /)`
- `resource.setrlimit(resource, limits, /)`

Классы (1):

- `resource.struct_rusage(iterable=(), /)` — count, index, n_fields, n_sequence_fields, n_unnamed_fields, ru_idrss, ru_inblock, ru_isrss, ru_ixrss, ru_majflt, ru_maxrss, ru_minflt, ru_msgrcv, ru_msgsnd, ru_nivcsw, ru_nsignals, ru_nswap, ru_nvcsw, ru_oublock, ru_stime, ru_utime

Исключения: `resource.error`.

Константы и данные: `resource.RLIMIT_AS` = `9`, `resource.RLIMIT_CORE` = `4`, `resource.RLIMIT_CPU` = `0`, `resource.RLIMIT_DATA` = `2`, `resource.RLIMIT_FSIZE` = `1`, `resource.RLIMIT_MEMLOCK` = `8`, `resource.RLIMIT_MSGQUEUE` = `12`, `resource.RLIMIT_NICE` = `13`, `resource.RLIMIT_NOFILE` = `7`, `resource.RLIMIT_NPROC` = `6`, `resource.RLIMIT_OFILE` = `7`, `resource.RLIMIT_RSS` = `5`, `resource.RLIMIT_RTPRIO` = `14`, `resource.RLIMIT_RTTIME` = `15`, `resource.RLIMIT_SIGPENDING` = `11`, `resource.RLIMIT_STACK` = `3`, `resource.RLIM_INFINITY` = `-1`, `resource.RUSAGE_CHILDREN` = `-1`, `resource.RUSAGE_SELF` = `0`, `resource.RUSAGE_THREAD` = `1`.

### secrets

_Python · импорт 75 мс_

> Generate cryptographically strong pseudo-random numbers suitable for

Функции (7):

- `secrets.choice(seq)`
- `secrets.compare_digest(a, b, /)`
- `secrets.randbelow(exclusive_upper_bound)`
- `secrets.randbits(k)`
- `secrets.token_bytes(nbytes=None)`
- `secrets.token_hex(nbytes=None)`
- `secrets.token_urlsafe(nbytes=None)`

Классы (1):

- `secrets.SystemRandom(x=None)` — VERSION, betavariate, binomialvariate, choice, choices, expovariate, gammavariate, gauss, getrandbits, getstate, lognormvariate, normalvariate, paretovariate, randbytes, randint, random, randrange, sample, seed, setstate, shuffle, triangular, uniform, vonmisesvariate, weibullvariate

### select

_расширение (.so) · импорт 3.0 мс_

> This module supports asynchronous I/O on multiple file descriptors.

Функции (2):

- `select.poll()`
- `select.select(rlist, wlist, xlist, timeout=None, /)`

Классы (1):

- `select.epoll(…)` — close, closed, fileno, fromfd, modify, poll, register, unregister

Исключения: `select.error`.

Константы и данные: `select.EPOLLERR` = `8`, `select.EPOLLET` = `2147483648`, `select.EPOLLEXCLUSIVE` = `268435456`, `select.EPOLLHUP` = `16`, `select.EPOLLIN` = `1`, `select.EPOLLMSG` = `1024`, `select.EPOLLONESHOT` = `1073741824`, `select.EPOLLOUT` = `4`, `select.EPOLLPRI` = `2`, `select.EPOLLRDBAND` = `128`, `select.EPOLLRDHUP` = `8192`, `select.EPOLLRDNORM` = `64`, `select.EPOLLWRBAND` = `512`, `select.EPOLLWRNORM` = `256`, `select.EPOLL_CLOEXEC` = `524288`, `select.PIPE_BUF` = `4096`, `select.POLLERR` = `8`, `select.POLLHUP` = `16`, `select.POLLIN` = `1`, `select.POLLMSG` = `1024`, `select.POLLNVAL` = `32`, `select.POLLOUT` = `4`, `select.POLLPRI` = `2`, `select.POLLRDBAND` = `128`, `select.POLLRDHUP` = `8192`, `select.POLLRDNORM` = `64`, `select.POLLWRBAND` = `512`, `select.POLLWRNORM` = `256`.

### selectors

_Python · импорт 18 мс_

> Selectors module.

Классы (6):

- `selectors.BaseSelector()` — close, get_key, get_map, modify, register, select, unregister
- `selectors.DefaultSelector()` — close, fileno, get_key, get_map, modify, register, select, unregister
- `selectors.EpollSelector()` — close, fileno, get_key, get_map, modify, register, select, unregister
- `selectors.PollSelector()` — close, get_key, get_map, modify, register, select, unregister
- `selectors.SelectSelector()` — close, get_key, get_map, modify, register, select, unregister
- `selectors.SelectorKey(fileobj, fd, events, data)` — count, data, events, fd, fileobj, index

Константы и данные: `selectors.EVENT_READ` = `1`, `selectors.EVENT_WRITE` = `2`.

### shelve

_Python · импорт 37 мс_

> Manage shelves of pickled objects.

Функции (1):

- `shelve.open(filename, flag='c', protocol=None, writeback=False)`

Классы (3):

- `shelve.BsdDbShelf(dict, protocol=None, writeback=False, keyencoding='utf-8')` — clear, close, first, get, items, keys, last, next, pop, popitem, previous, set_location, setdefault, sync, update, values
- `shelve.DbfilenameShelf(filename, flag='c', protocol=None, writeback=False)` — clear, close, get, items, keys, pop, popitem, setdefault, sync, update, values
- `shelve.Shelf(dict, protocol=None, writeback=False, keyencoding='utf-8')` — clear, close, get, items, keys, pop, popitem, setdefault, sync, update, values

### shutil

_Python · импорт 41 мс_

> Utility functions for copying and archiving files and directory trees.

Функции (22):

- `shutil.chown(path, user=None, group=None, *, dir_fd=None, follow_symlinks=True)`
- `shutil.copy(src, dst, *, follow_symlinks=True)`
- `shutil.copy2(src, dst, *, follow_symlinks=True)`
- `shutil.copyfile(src, dst, *, follow_symlinks=True)`
- `shutil.copyfileobj(fsrc, fdst, length=0)`
- `shutil.copymode(src, dst, *, follow_symlinks=True)`
- `shutil.copystat(src, dst, *, follow_symlinks=True)`
- `shutil.copytree(src, dst, symlinks=False, ignore=None, copy_function=<function copy2>, ignore_dangling_symlinks=False, dirs_exist_ok=False)`
- `shutil.disk_usage(path)`
- `shutil.get_archive_formats()`
- `shutil.get_terminal_size(fallback=(80, 24))`
- `shutil.get_unpack_formats()`
- `shutil.ignore_patterns(*patterns)`
- `shutil.make_archive(base_name, format, root_dir=None, base_dir=None, verbose=0, dry_run=0, owner=None, group=None, logger=None)`
- `shutil.move(src, dst, copy_function=<function copy2>)`
- `shutil.register_archive_format(name, function, extra_args=None, description='')`
- `shutil.register_unpack_format(name, extensions, function, extra_args=None, description='')`
- `shutil.rmtree(path, ignore_errors=False, onerror=None, *, onexc=None, dir_fd=None)`
- `shutil.unpack_archive(filename, extract_dir=None, format=None, *, filter=None)`
- `shutil.unregister_archive_format(name)`
- `shutil.unregister_unpack_format(name)`
- `shutil.which(cmd, mode=1, path=None)`

Исключения: `shutil.Error`, `shutil.ExecError`, `shutil.SameFileError`, `shutil.SpecialFileError`.

### smtplib

_Python · импорт 407 мс_

> SMTP/ESMTP client class.

Функции (2):

- `smtplib.quoteaddr(addrstring)`
- `smtplib.quotedata(data)`

Классы (2):

- `smtplib.SMTP(host='', port=0, local_hostname=None, timeout=<object object>, source_address=None)` — auth, auth_cram_md5, auth_login, auth_plain, close, connect, data, debuglevel, default_port, docmd, does_esmtp, ehlo, ehlo_msg, ehlo_or_helo_if_needed, ehlo_resp, expn, file, getreply, has_extn, helo, helo_resp, help, login, mail, noop, putcmd, quit, rcpt, rset, send, send_message, sendmail, set_debuglevel, sock, starttls, verify, vrfy
- `smtplib.SMTP_SSL(host='', port=0, local_hostname=None, *, timeout=<object object>, source_address=None, context=None)` — auth, auth_cram_md5, auth_login, auth_plain, close, connect, data, debuglevel, default_port, docmd, does_esmtp, ehlo, ehlo_msg, ehlo_or_helo_if_needed, ehlo_resp, expn, file, getreply, has_extn, helo, helo_resp, help, login, mail, noop, putcmd, quit, rcpt, rset, send, send_message, sendmail, set_debuglevel, sock, starttls, verify, vrfy

Исключения: `smtplib.SMTPAuthenticationError`, `smtplib.SMTPConnectError`, `smtplib.SMTPDataError`, `smtplib.SMTPException`, `smtplib.SMTPHeloError`, `smtplib.SMTPNotSupportedError`, `smtplib.SMTPRecipientsRefused`, `smtplib.SMTPResponseException`, `smtplib.SMTPSenderRefused`, `smtplib.SMTPServerDisconnected`.

### socket

_Python · импорт 53 мс_

> This module provides socket operations and some related functions.

Функции (35):

- `socket.CMSG_LEN(length) -> control message length`
- `socket.CMSG_SPACE(length) -> buffer size`
- `socket.close(object, /)`
- `socket.create_connection(address, timeout=<object object>, source_address=None, *, all_errors=False)`
- `socket.create_server(address, *, family=<AddressFamily.AF_INET: 2>, backlog=None, reuse_port=False, dualstack_ipv6=False)`
- `socket.dup(object, /)`
- `socket.fromfd(fd, family, type, proto=0)`
- `socket.getaddrinfo(host, port, family=0, type=0, proto=0, flags=0)`
- `socket.getdefaulttimeout()`
- `socket.getfqdn(name='')`
- `socket.gethostbyaddr(host) -> (name, aliaslist, addresslist)`
- `socket.gethostbyname(host) -> address`
- `socket.gethostbyname_ex(host) -> (name, aliaslist, addresslist)`
- `socket.gethostname()`
- `socket.getnameinfo(sockaddr, flags) --> (host, port)`
- `socket.getprotobyname(name) -> integer`
- `socket.getservbyname(servicename[, protocolname]) -> integer`
- `socket.getservbyport(port[, protocolname]) -> string`
- `socket.has_dualstack_ipv6()`
- `socket.htonl(object, /)`
- `socket.htons(x, /)`
- `socket.if_indextoname(object, /)`
- `socket.if_nameindex()`
- `socket.if_nametoindex(oname, /)`
- `socket.inet_aton(ip_addr, /)`
- `socket.inet_ntoa(packed_ip, /)`
- `socket.inet_ntop(af, packed_ip) -> string formatted IP address`
- `socket.inet_pton(af, ip) -> packed IP address string`
- `socket.ntohl(object, /)`
- `socket.ntohs(x, /)`
- `socket.recv_fds(sock, bufsize, maxfds, flags=0)`
- `socket.send_fds(sock, buffers, fds, flags=0, address=None)`
- `socket.setdefaulttimeout(object, /)`
- `socket.sethostname(name)`
- `socket.socketpair(family=None, type=<SocketKind.SOCK_STREAM: 1>, proto=0)`

Классы (4):

- `socket.AddressFamily(*values)` — AF_ALG, AF_APPLETALK, AF_ASH, AF_ATMPVC, AF_ATMSVC, AF_AX25, AF_BRIDGE, AF_CAN, AF_ECONET, AF_INET, AF_INET6, AF_IPX, AF_IRDA, AF_KEY, AF_LLC, AF_NETBEUI, AF_NETLINK, AF_NETROM, AF_PACKET, AF_PPPOX, AF_RDS, AF_ROSE, AF_SECURITY, AF_SNA, AF_TIPC, AF_UNIX, AF_UNSPEC, AF_VSOCK, AF_WANPIPE, AF_X25, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `socket.SocketKind(*values)` — SOCK_CLOEXEC, SOCK_DGRAM, SOCK_NONBLOCK, SOCK_RAW, SOCK_RDM, SOCK_SEQPACKET, SOCK_STREAM, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `socket.SocketType(family=AF_INET, type=SOCK_STREAM, proto=0) -> socket object` — bind, close, connect, connect_ex, detach, family, fileno, getblocking, getpeername, getsockname, getsockopt, gettimeout, listen, proto, recv, recv_into, recvfrom, recvfrom_into, recvmsg, recvmsg_into, send, sendall, sendmsg, sendmsg_afalg, sendto, setblocking, setsockopt, settimeout, shutdown, timeout, type
- `socket.socket(family=-1, type=-1, proto=-1, fileno=None)` — accept, bind, close, connect, connect_ex, detach, dup, family, fileno, get_inheritable, getblocking, getpeername, getsockname, getsockopt, gettimeout, listen, makefile, proto, recv, recv_into, recvfrom, recvfrom_into, recvmsg, recvmsg_into, send, sendall, sendfile, sendmsg, sendmsg_afalg, sendto, set_inheritable, setblocking, setsockopt, settimeout, shutdown, timeout, type

Исключения: `socket.error`, `socket.gaierror`, `socket.herror`, `socket.timeout`.

Константы и данные (340): `AF_ALG`, `AF_APPLETALK`, `AF_ASH`, `AF_ATMPVC`, `AF_ATMSVC`, `AF_AX25`, `AF_BRIDGE`, `AF_CAN`, `AF_DECnet`, `AF_ECONET`, `AF_INET`, `AF_INET6`, `AF_IPX`, `AF_IRDA`, `AF_KEY`, `AF_LLC`, `AF_NETBEUI`, `AF_NETLINK`, `AF_NETROM`, `AF_PACKET`, `AF_PPPOX`, `AF_RDS`, `AF_ROSE`, `AF_ROUTE`, `AF_SECURITY`, `AF_SNA`, `AF_TIPC`, `AF_UNIX`, `AF_UNSPEC`, `AF_VSOCK`, `AF_WANPIPE`, `AF_X25`, `AI_ADDRCONFIG`, `AI_ALL`, `AI_CANONNAME`, `AI_NUMERICHOST`, `AI_NUMERICSERV`, `AI_PASSIVE`, `AI_V4MAPPED`, `ALG_OP_DECRYPT`, `ALG_OP_ENCRYPT`, `ALG_OP_SIGN`, `ALG_OP_VERIFY`, `ALG_SET_AEAD_ASSOCLEN`, `ALG_SET_AEAD_AUTHSIZE`, `ALG_SET_IV`, `ALG_SET_KEY`, `ALG_SET_OP`, `ALG_SET_PUBKEY`, `CAN_BCM`, `CAN_BCM_CAN_FD_FRAME`, `CAN_BCM_RX_ANNOUNCE_RESUME`, `CAN_BCM_RX_CHANGED`, `CAN_BCM_RX_CHECK_DLC`, `CAN_BCM_RX_DELETE`, `CAN_BCM_RX_FILTER_ID`, `CAN_BCM_RX_NO_AUTOTIMER`, `CAN_BCM_RX_READ`, `CAN_BCM_RX_RTR_FRAME`, `CAN_BCM_RX_SETUP`, `CAN_BCM_RX_STATUS`, `CAN_BCM_RX_TIMEOUT`, `CAN_BCM_SETTIMER`, `CAN_BCM_STARTTIMER`, `CAN_BCM_TX_ANNOUNCE`, `CAN_BCM_TX_COUNTEVT`, `CAN_BCM_TX_CP_CAN_ID`, `CAN_BCM_TX_DELETE`, `CAN_BCM_TX_EXPIRED`, `CAN_BCM_TX_READ`, `CAN_BCM_TX_RESET_MULTI_IDX`, `CAN_BCM_TX_SEND`, `CAN_BCM_TX_SETUP`, `CAN_BCM_TX_STATUS`, `CAN_EFF_FLAG`, `CAN_EFF_MASK`, `CAN_ERR_FLAG`, `CAN_ERR_MASK`, `CAN_ISOTP`, `CAN_RAW`, `CAN_RAW_ERR_FILTER`, `CAN_RAW_FD_FRAMES`, `CAN_RAW_FILTER`, `CAN_RAW_JOIN_FILTERS`, `CAN_RAW_LOOPBACK`, `CAN_RAW_RECV_OWN_MSGS`, `CAN_RTR_FLAG`, `CAN_SFF_MASK`, `CAPI`, `EAI_ADDRFAMILY`, `EAI_AGAIN`, `EAI_BADFLAGS`, `EAI_FAIL`, `EAI_FAMILY`, `EAI_MEMORY`, `EAI_NODATA`, `EAI_NONAME`, `EAI_OVERFLOW`, `EAI_SERVICE`, `EAI_SOCKTYPE`, `EAI_SYSTEM`, `ETHERTYPE_ARP`, `ETHERTYPE_IP`, `ETHERTYPE_IPV6`, `ETHERTYPE_VLAN`, `ETH_P_ALL`, `INADDR_ALLHOSTS_GROUP`, `INADDR_ANY`, `INADDR_BROADCAST`, `INADDR_LOOPBACK`, `INADDR_MAX_LOCAL_GROUP`, `INADDR_NONE`, `INADDR_UNSPEC_GROUP`, `IOCTL_VM_SOCKETS_GET_LOCAL_CID`, `IPPORT_RESERVED`, `IPPORT_USERRESERVED`, `IPPROTO_AH`, `IPPROTO_DSTOPTS`, `IPPROTO_EGP`, `IPPROTO_ESP`, `IPPROTO_FRAGMENT`, `IPPROTO_GRE`, `IPPROTO_HOPOPTS`, `IPPROTO_ICMP`, `IPPROTO_ICMPV6`, `IPPROTO_IDP`, `IPPROTO_IGMP`, `IPPROTO_IP`, `IPPROTO_IPIP`, `IPPROTO_IPV6`, `IPPROTO_NONE`, `IPPROTO_PIM`, `IPPROTO_PUP`, `IPPROTO_RAW`, `IPPROTO_ROUTING`, `IPPROTO_RSVP`, `IPPROTO_SCTP`, `IPPROTO_TCP`, `IPPROTO_TP`, `IPPROTO_UDP`, `IPPROTO_UDPLITE`, `IPV6_CHECKSUM`, `IPV6_DONTFRAG`, `IPV6_DSTOPTS`, `IPV6_HOPLIMIT`, `IPV6_HOPOPTS`, `IPV6_JOIN_GROUP`, `IPV6_LEAVE_GROUP`, `IPV6_MULTICAST_HOPS`, `IPV6_MULTICAST_IF`, `IPV6_MULTICAST_LOOP`, `IPV6_NEXTHOP`, `IPV6_PATHMTU`, `IPV6_PKTINFO`, `IPV6_RECVDSTOPTS`, `IPV6_RECVHOPLIMIT`, `IPV6_RECVHOPOPTS`, `IPV6_RECVPATHMTU`, `IPV6_RECVPKTINFO`, `IPV6_RECVRTHDR`, `IPV6_RECVTCLASS`, `IPV6_RTHDR`, `IPV6_RTHDRDSTOPTS`, `IPV6_RTHDR_TYPE_0`, `IPV6_TCLASS`, `IPV6_UNICAST_HOPS`, `IPV6_V6ONLY`, `IP_ADD_MEMBERSHIP`, `IP_ADD_SOURCE_MEMBERSHIP`, `IP_BIND_ADDRESS_NO_PORT`, `IP_BLOCK_SOURCE`, `IP_DEFAULT_MULTICAST_LOOP`, `IP_DEFAULT_MULTICAST_TTL`, `IP_DROP_MEMBERSHIP`, `IP_DROP_SOURCE_MEMBERSHIP`, `IP_HDRINCL`, `IP_MAX_MEMBERSHIPS`, `IP_MULTICAST_IF`, `IP_MULTICAST_LOOP`, `IP_MULTICAST_TTL`, `IP_OPTIONS`, `IP_PKTINFO`, `IP_RECVOPTS`, `IP_RECVRETOPTS`, `IP_RECVTOS`, `IP_RETOPTS`, `IP_TOS`, `IP_TRANSPARENT`, `IP_TTL`, `IP_UNBLOCK_SOURCE`, `MSG_CMSG_CLOEXEC`, `MSG_CONFIRM`, `MSG_CTRUNC`, `MSG_DONTROUTE`, `MSG_DONTWAIT`, `MSG_EOR`, `MSG_ERRQUEUE`, `MSG_FASTOPEN`, `MSG_MORE`, `MSG_NOSIGNAL`, `MSG_OOB`, `MSG_PEEK`, `MSG_TRUNC`, `MSG_WAITALL`, `NETLINK_CRYPTO`, `NETLINK_DNRTMSG`, `NETLINK_FIREWALL`, `NETLINK_IP6_FW`, `NETLINK_NFLOG`, `NETLINK_ROUTE`, `NETLINK_USERSOCK`, `NETLINK_XFRM`, `NI_DGRAM`, `NI_IDN`, `NI_MAXHOST`, `NI_MAXSERV`, `NI_NAMEREQD`, `NI_NOFQDN`, `NI_NUMERICHOST`, `NI_NUMERICSERV`, `PACKET_BROADCAST`, `PACKET_FASTROUTE`, `PACKET_HOST`, `PACKET_LOOPBACK`, `PACKET_MULTICAST`, `PACKET_OTHERHOST`, `PACKET_OUTGOING`, `PF_CAN`, `PF_PACKET`, `PF_RDS`, `SCM_CREDENTIALS`, `SCM_RIGHTS`, `SHUT_RD`, `SHUT_RDWR`, `SHUT_WR`, `SOCK_CLOEXEC`, `SOCK_DGRAM`, `SOCK_NONBLOCK`, `SOCK_RAW`, `SOCK_RDM`, `SOCK_SEQPACKET`, `SOCK_STREAM`, `SOL_ALG`, `SOL_CAN_BASE`, `SOL_CAN_RAW`, `SOL_IP`, `SOL_RDS`, `SOL_SOCKET`, `SOL_TCP`, `SOL_TIPC`, `SOL_UDP`, `SOMAXCONN`, `SO_ACCEPTCONN`, `SO_BINDTODEVICE`, `SO_BROADCAST`, `SO_DEBUG`, `SO_DOMAIN`, `SO_DONTROUTE`, `SO_ERROR`, `SO_INCOMING_CPU`, `SO_KEEPALIVE`, `SO_LINGER`, `SO_MARK`, `SO_OOBINLINE`, `SO_PASSCRED`, `SO_PASSSEC`, `SO_PEERCRED`, `SO_PEERSEC`, `SO_PRIORITY`, `SO_PROTOCOL`, `SO_RCVBUF`, `SO_RCVLOWAT`, `SO_RCVTIMEO`, `SO_REUSEADDR`, `SO_REUSEPORT`, `SO_SNDBUF`, `SO_SNDLOWAT`, `SO_SNDTIMEO`, `SO_TYPE`, `SO_VM_SOCKETS_BUFFER_MAX_SIZE`, `SO_VM_SOCKETS_BUFFER_MIN_SIZE`, `SO_VM_SOCKETS_BUFFER_SIZE`, `TCP_CC_INFO`, `TCP_CONGESTION`, `TCP_CORK`, `TCP_DEFER_ACCEPT`, `TCP_FASTOPEN`, `TCP_INFO`, `TCP_KEEPCNT`, `TCP_KEEPIDLE`, `TCP_KEEPINTVL`, `TCP_LINGER2`, `TCP_MAXSEG`, `TCP_MD5SIG`, `TCP_NODELAY`, `TCP_NOTSENT_LOWAT`, `TCP_QUEUE_SEQ`, `TCP_QUICKACK`, `TCP_REPAIR`, `TCP_REPAIR_OPTIONS`, `TCP_REPAIR_QUEUE`, `TCP_REPAIR_WINDOW`, `TCP_SAVED_SYN`, `TCP_SAVE_SYN`, `TCP_SYNCNT`, `TCP_THIN_DUPACK`, `TCP_THIN_LINEAR_TIMEOUTS`, `TCP_TIMESTAMP`, `TCP_USER_TIMEOUT`, `TCP_WINDOW_CLAMP`, `TIPC_ADDR_ID`, `TIPC_ADDR_NAME`, `TIPC_ADDR_NAMESEQ`, `TIPC_CFG_SRV`, `TIPC_CLUSTER_SCOPE`, `TIPC_CONN_TIMEOUT`, `TIPC_CRITICAL_IMPORTANCE`, `TIPC_DEST_DROPPABLE`, `TIPC_HIGH_IMPORTANCE`, `TIPC_IMPORTANCE`, `TIPC_LOW_IMPORTANCE`, `TIPC_MEDIUM_IMPORTANCE`, `TIPC_NODE_SCOPE`, `TIPC_PUBLISHED`, `TIPC_SRC_DROPPABLE`, `TIPC_SUBSCR_TIMEOUT`, `TIPC_SUB_CANCEL`, `TIPC_SUB_PORTS`, `TIPC_SUB_SERVICE`, `TIPC_TOP_SRV`, `TIPC_WAIT_FOREVER`, `TIPC_WITHDRAWN`, `TIPC_ZONE_SCOPE`, `UDPLITE_RECV_CSCOV`, `UDPLITE_SEND_CSCOV`, `VMADDR_CID_ANY`, `VMADDR_CID_HOST`, `VMADDR_PORT_ANY`, `VM_SOCKETS_INVALID_VERSION`, `has_ipv6`.

### socketserver

_Python · импорт 89 мс_

> Generic socket server classes.

Классы (18):

- `socketserver.BaseRequestHandler(request, client_address, server)` — finish, handle, setup
- `socketserver.BaseServer(server_address, RequestHandlerClass)` — close_request, finish_request, handle_error, handle_request, handle_timeout, process_request, serve_forever, server_activate, server_close, service_actions, shutdown, shutdown_request, timeout, verify_request
- `socketserver.DatagramRequestHandler(request, client_address, server)` — finish, handle, setup
- `socketserver.ForkingMixIn()` — active_children, block_on_close, collect_children, handle_timeout, max_children, process_request, server_close, service_actions, timeout
- `socketserver.ForkingTCPServer(server_address, RequestHandlerClass, bind_and_activate=True)` — active_children, address_family, allow_reuse_address, allow_reuse_port, block_on_close, close_request, collect_children, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, max_children, process_request, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.ForkingUDPServer(server_address, RequestHandlerClass, bind_and_activate=True)` — active_children, address_family, allow_reuse_address, allow_reuse_port, block_on_close, close_request, collect_children, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, max_children, max_packet_size, process_request, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.ForkingUnixDatagramServer(server_address, RequestHandlerClass, bind_and_activate=True)` — active_children, address_family, allow_reuse_address, allow_reuse_port, block_on_close, close_request, collect_children, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, max_children, max_packet_size, process_request, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.ForkingUnixStreamServer(server_address, RequestHandlerClass, bind_and_activate=True)` — active_children, address_family, allow_reuse_address, allow_reuse_port, block_on_close, close_request, collect_children, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, max_children, process_request, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.StreamRequestHandler(request, client_address, server)` — disable_nagle_algorithm, finish, handle, rbufsize, setup, timeout, wbufsize
- `socketserver.TCPServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, close_request, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, process_request, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.ThreadingMixIn()` — block_on_close, daemon_threads, process_request, process_request_thread, server_close
- `socketserver.ThreadingTCPServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, block_on_close, close_request, daemon_threads, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, process_request, process_request_thread, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.ThreadingUDPServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, block_on_close, close_request, daemon_threads, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, max_packet_size, process_request, process_request_thread, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.ThreadingUnixDatagramServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, block_on_close, close_request, daemon_threads, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, max_packet_size, process_request, process_request_thread, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.ThreadingUnixStreamServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, block_on_close, close_request, daemon_threads, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, process_request, process_request_thread, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.UDPServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, close_request, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, max_packet_size, process_request, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.UnixDatagramServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, close_request, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, max_packet_size, process_request, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request
- `socketserver.UnixStreamServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, close_request, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, process_request, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, timeout, verify_request

### sqlite3

_пакет · импорт 27 мс_

> The sqlite3 extension module provides a DB-API 2.0 (PEP 249) compliant

Подмодули: `sqlite3.dbapi2`, `sqlite3.dump`.

Функции (9):

- `sqlite3.DateFromTicks(ticks)`
- `sqlite3.TimeFromTicks(ticks)`
- `sqlite3.TimestampFromTicks(ticks)`
- `sqlite3.adapt(…)`
- `sqlite3.complete_statement(statement)`
- `sqlite3.connect(…)`
- `sqlite3.enable_callback_tracebacks(enable, /)`
- `sqlite3.register_adapter(type, adapter, /)`
- `sqlite3.register_converter(typename, converter, /)`

Классы (6):

- `sqlite3.Binary(object)` — c_contiguous, cast, contiguous, f_contiguous, format, hex, itemsize, nbytes, ndim, obj, readonly, release, shape, strides, suboffsets, tobytes, tolist, toreadonly
- `sqlite3.Blob()` — close, read, seek, tell, write
- `sqlite3.Connection(…)` — DataError, DatabaseError, Error, IntegrityError, InterfaceError, InternalError, NotSupportedError, OperationalError, ProgrammingError, Warning, autocommit, backup, blobopen, close, commit, create_aggregate, create_collation, create_function, create_window_function, cursor, execute, executemany, executescript, getconfig, getlimit, in_transaction, interrupt, isolation_level, iterdump, rollback, row_factory, set_authorizer, set_progress_handler, set_trace_callback, setconfig, setlimit, text_factory, total_changes
- `sqlite3.Cursor(…)` — arraysize, close, connection, description, execute, executemany, executescript, fetchall, fetchmany, fetchone, lastrowid, row_factory, rowcount, setinputsizes, setoutputsize
- `sqlite3.PrepareProtocol(…)`
- `sqlite3.Row(…)` — keys

Исключения: `sqlite3.DataError`, `sqlite3.DatabaseError`, `sqlite3.Error`, `sqlite3.IntegrityError`, `sqlite3.InterfaceError`, `sqlite3.InternalError`, `sqlite3.NotSupportedError`, `sqlite3.OperationalError`, `sqlite3.ProgrammingError`, `sqlite3.Warning`.

Константы и данные (164): `LEGACY_TRANSACTION_CONTROL`, `PARSE_COLNAMES`, `PARSE_DECLTYPES`, `SQLITE_ABORT`, `SQLITE_ABORT_ROLLBACK`, `SQLITE_ALTER_TABLE`, `SQLITE_ANALYZE`, `SQLITE_ATTACH`, `SQLITE_AUTH`, `SQLITE_AUTH_USER`, `SQLITE_BUSY`, `SQLITE_BUSY_RECOVERY`, `SQLITE_BUSY_SNAPSHOT`, `SQLITE_CANTOPEN`, `SQLITE_CANTOPEN_CONVPATH`, `SQLITE_CANTOPEN_DIRTYWAL`, `SQLITE_CANTOPEN_FULLPATH`, `SQLITE_CANTOPEN_ISDIR`, `SQLITE_CANTOPEN_NOTEMPDIR`, `SQLITE_CONSTRAINT`, `SQLITE_CONSTRAINT_CHECK`, `SQLITE_CONSTRAINT_COMMITHOOK`, `SQLITE_CONSTRAINT_FOREIGNKEY`, `SQLITE_CONSTRAINT_FUNCTION`, `SQLITE_CONSTRAINT_NOTNULL`, `SQLITE_CONSTRAINT_PRIMARYKEY`, `SQLITE_CONSTRAINT_ROWID`, `SQLITE_CONSTRAINT_TRIGGER`, `SQLITE_CONSTRAINT_UNIQUE`, `SQLITE_CONSTRAINT_VTAB`, `SQLITE_CORRUPT`, `SQLITE_CORRUPT_SEQUENCE`, `SQLITE_CORRUPT_VTAB`, `SQLITE_CREATE_INDEX`, `SQLITE_CREATE_TABLE`, `SQLITE_CREATE_TEMP_INDEX`, `SQLITE_CREATE_TEMP_TABLE`, `SQLITE_CREATE_TEMP_TRIGGER`, `SQLITE_CREATE_TEMP_VIEW`, `SQLITE_CREATE_TRIGGER`, `SQLITE_CREATE_VIEW`, `SQLITE_CREATE_VTABLE`, `SQLITE_DBCONFIG_DEFENSIVE`, `SQLITE_DBCONFIG_ENABLE_FKEY`, `SQLITE_DBCONFIG_ENABLE_FTS3_TOKENIZER`, `SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION`, `SQLITE_DBCONFIG_ENABLE_QPSG`, `SQLITE_DBCONFIG_ENABLE_TRIGGER`, `SQLITE_DBCONFIG_NO_CKPT_ON_CLOSE`, `SQLITE_DBCONFIG_RESET_DATABASE`, `SQLITE_DBCONFIG_TRIGGER_EQP`, `SQLITE_DELETE`, `SQLITE_DENY`, `SQLITE_DETACH`, `SQLITE_DONE`, `SQLITE_DROP_INDEX`, `SQLITE_DROP_TABLE`, `SQLITE_DROP_TEMP_INDEX`, `SQLITE_DROP_TEMP_TABLE`, `SQLITE_DROP_TEMP_TRIGGER`, `SQLITE_DROP_TEMP_VIEW`, `SQLITE_DROP_TRIGGER`, `SQLITE_DROP_VIEW`, `SQLITE_DROP_VTABLE`, `SQLITE_EMPTY`, `SQLITE_ERROR`, `SQLITE_ERROR_MISSING_COLLSEQ`, `SQLITE_ERROR_RETRY`, `SQLITE_ERROR_SNAPSHOT`, `SQLITE_FORMAT`, `SQLITE_FULL`, `SQLITE_FUNCTION`, `SQLITE_IGNORE`, `SQLITE_INSERT`, `SQLITE_INTERNAL`, `SQLITE_INTERRUPT`, `SQLITE_IOERR`, `SQLITE_IOERR_ACCESS`, `SQLITE_IOERR_AUTH`, `SQLITE_IOERR_BEGIN_ATOMIC`, `SQLITE_IOERR_BLOCKED`, `SQLITE_IOERR_CHECKRESERVEDLOCK`, `SQLITE_IOERR_CLOSE`, `SQLITE_IOERR_COMMIT_ATOMIC`, `SQLITE_IOERR_CONVPATH`, `SQLITE_IOERR_DELETE`, `SQLITE_IOERR_DELETE_NOENT`, `SQLITE_IOERR_DIR_CLOSE`, `SQLITE_IOERR_DIR_FSYNC`, `SQLITE_IOERR_FSTAT`, `SQLITE_IOERR_FSYNC`, `SQLITE_IOERR_GETTEMPPATH`, `SQLITE_IOERR_LOCK`, `SQLITE_IOERR_MMAP`, `SQLITE_IOERR_NOMEM`, `SQLITE_IOERR_RDLOCK`, `SQLITE_IOERR_READ`, `SQLITE_IOERR_ROLLBACK_ATOMIC`, `SQLITE_IOERR_SEEK`, `SQLITE_IOERR_SHMLOCK`, `SQLITE_IOERR_SHMMAP`, `SQLITE_IOERR_SHMOPEN`, `SQLITE_IOERR_SHMSIZE`, `SQLITE_IOERR_SHORT_READ`, `SQLITE_IOERR_TRUNCATE`, `SQLITE_IOERR_UNLOCK`, `SQLITE_IOERR_VNODE`, `SQLITE_IOERR_WRITE`, `SQLITE_LIMIT_ATTACHED`, `SQLITE_LIMIT_COLUMN`, `SQLITE_LIMIT_COMPOUND_SELECT`, `SQLITE_LIMIT_EXPR_DEPTH`, `SQLITE_LIMIT_FUNCTION_ARG`, `SQLITE_LIMIT_LENGTH`, `SQLITE_LIMIT_LIKE_PATTERN_LENGTH`, `SQLITE_LIMIT_SQL_LENGTH`, `SQLITE_LIMIT_TRIGGER_DEPTH`, `SQLITE_LIMIT_VARIABLE_NUMBER`, `SQLITE_LIMIT_VDBE_OP`, `SQLITE_LIMIT_WORKER_THREADS`, `SQLITE_LOCKED`, `SQLITE_LOCKED_SHAREDCACHE`, `SQLITE_LOCKED_VTAB`, `SQLITE_MISMATCH`, `SQLITE_MISUSE`, `SQLITE_NOLFS`, `SQLITE_NOMEM`, `SQLITE_NOTADB`, `SQLITE_NOTFOUND`, `SQLITE_NOTICE`, `SQLITE_NOTICE_RECOVER_ROLLBACK`, `SQLITE_NOTICE_RECOVER_WAL`, `SQLITE_OK`, `SQLITE_OK_LOAD_PERMANENTLY`, `SQLITE_PERM`, `SQLITE_PRAGMA`, `SQLITE_PROTOCOL`, `SQLITE_RANGE`, `SQLITE_READ`, `SQLITE_READONLY`, `SQLITE_READONLY_CANTINIT`, `SQLITE_READONLY_CANTLOCK`, `SQLITE_READONLY_DBMOVED`, `SQLITE_READONLY_DIRECTORY`, `SQLITE_READONLY_RECOVERY`, `SQLITE_READONLY_ROLLBACK`, `SQLITE_RECURSIVE`, `SQLITE_REINDEX`, `SQLITE_ROW`, `SQLITE_SAVEPOINT`, `SQLITE_SCHEMA`, `SQLITE_SELECT`, `SQLITE_TOOBIG`, `SQLITE_TRANSACTION`, `SQLITE_UPDATE`, `SQLITE_WARNING`, `SQLITE_WARNING_AUTOINDEX`, `adapters`, `apilevel`, `converters`, `paramstyle`, `sqlite_version`, `sqlite_version_info`, `threadsafety`.

### sqlite3.dbapi2

_подмодуль · импорт 37 мс_

Функции (9):

- `sqlite3.dbapi2.DateFromTicks(ticks)`
- `sqlite3.dbapi2.TimeFromTicks(ticks)`
- `sqlite3.dbapi2.TimestampFromTicks(ticks)`
- `sqlite3.dbapi2.adapt(…)`
- `sqlite3.dbapi2.complete_statement(statement)`
- `sqlite3.dbapi2.connect(…)`
- `sqlite3.dbapi2.enable_callback_tracebacks(enable, /)`
- `sqlite3.dbapi2.register_adapter(type, adapter, /)`
- `sqlite3.dbapi2.register_converter(typename, converter, /)`

Классы (6):

- `sqlite3.dbapi2.Binary(object)` — c_contiguous, cast, contiguous, f_contiguous, format, hex, itemsize, nbytes, ndim, obj, readonly, release, shape, strides, suboffsets, tobytes, tolist, toreadonly
- `sqlite3.dbapi2.Blob()` — close, read, seek, tell, write
- `sqlite3.dbapi2.Connection(…)` — DataError, DatabaseError, Error, IntegrityError, InterfaceError, InternalError, NotSupportedError, OperationalError, ProgrammingError, Warning, autocommit, backup, blobopen, close, commit, create_aggregate, create_collation, create_function, create_window_function, cursor, execute, executemany, executescript, getconfig, getlimit, in_transaction, interrupt, isolation_level, iterdump, rollback, row_factory, set_authorizer, set_progress_handler, set_trace_callback, setconfig, setlimit, text_factory, total_changes
- `sqlite3.dbapi2.Cursor(…)` — arraysize, close, connection, description, execute, executemany, executescript, fetchall, fetchmany, fetchone, lastrowid, row_factory, rowcount, setinputsizes, setoutputsize
- `sqlite3.dbapi2.PrepareProtocol(…)`
- `sqlite3.dbapi2.Row(…)` — keys

Исключения: `sqlite3.dbapi2.DataError`, `sqlite3.dbapi2.DatabaseError`, `sqlite3.dbapi2.Error`, `sqlite3.dbapi2.IntegrityError`, `sqlite3.dbapi2.InterfaceError`, `sqlite3.dbapi2.InternalError`, `sqlite3.dbapi2.NotSupportedError`, `sqlite3.dbapi2.OperationalError`, `sqlite3.dbapi2.ProgrammingError`, `sqlite3.dbapi2.Warning`.

Константы и данные (164): `LEGACY_TRANSACTION_CONTROL`, `PARSE_COLNAMES`, `PARSE_DECLTYPES`, `SQLITE_ABORT`, `SQLITE_ABORT_ROLLBACK`, `SQLITE_ALTER_TABLE`, `SQLITE_ANALYZE`, `SQLITE_ATTACH`, `SQLITE_AUTH`, `SQLITE_AUTH_USER`, `SQLITE_BUSY`, `SQLITE_BUSY_RECOVERY`, `SQLITE_BUSY_SNAPSHOT`, `SQLITE_CANTOPEN`, `SQLITE_CANTOPEN_CONVPATH`, `SQLITE_CANTOPEN_DIRTYWAL`, `SQLITE_CANTOPEN_FULLPATH`, `SQLITE_CANTOPEN_ISDIR`, `SQLITE_CANTOPEN_NOTEMPDIR`, `SQLITE_CONSTRAINT`, `SQLITE_CONSTRAINT_CHECK`, `SQLITE_CONSTRAINT_COMMITHOOK`, `SQLITE_CONSTRAINT_FOREIGNKEY`, `SQLITE_CONSTRAINT_FUNCTION`, `SQLITE_CONSTRAINT_NOTNULL`, `SQLITE_CONSTRAINT_PRIMARYKEY`, `SQLITE_CONSTRAINT_ROWID`, `SQLITE_CONSTRAINT_TRIGGER`, `SQLITE_CONSTRAINT_UNIQUE`, `SQLITE_CONSTRAINT_VTAB`, `SQLITE_CORRUPT`, `SQLITE_CORRUPT_SEQUENCE`, `SQLITE_CORRUPT_VTAB`, `SQLITE_CREATE_INDEX`, `SQLITE_CREATE_TABLE`, `SQLITE_CREATE_TEMP_INDEX`, `SQLITE_CREATE_TEMP_TABLE`, `SQLITE_CREATE_TEMP_TRIGGER`, `SQLITE_CREATE_TEMP_VIEW`, `SQLITE_CREATE_TRIGGER`, `SQLITE_CREATE_VIEW`, `SQLITE_CREATE_VTABLE`, `SQLITE_DBCONFIG_DEFENSIVE`, `SQLITE_DBCONFIG_ENABLE_FKEY`, `SQLITE_DBCONFIG_ENABLE_FTS3_TOKENIZER`, `SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION`, `SQLITE_DBCONFIG_ENABLE_QPSG`, `SQLITE_DBCONFIG_ENABLE_TRIGGER`, `SQLITE_DBCONFIG_NO_CKPT_ON_CLOSE`, `SQLITE_DBCONFIG_RESET_DATABASE`, `SQLITE_DBCONFIG_TRIGGER_EQP`, `SQLITE_DELETE`, `SQLITE_DENY`, `SQLITE_DETACH`, `SQLITE_DONE`, `SQLITE_DROP_INDEX`, `SQLITE_DROP_TABLE`, `SQLITE_DROP_TEMP_INDEX`, `SQLITE_DROP_TEMP_TABLE`, `SQLITE_DROP_TEMP_TRIGGER`, `SQLITE_DROP_TEMP_VIEW`, `SQLITE_DROP_TRIGGER`, `SQLITE_DROP_VIEW`, `SQLITE_DROP_VTABLE`, `SQLITE_EMPTY`, `SQLITE_ERROR`, `SQLITE_ERROR_MISSING_COLLSEQ`, `SQLITE_ERROR_RETRY`, `SQLITE_ERROR_SNAPSHOT`, `SQLITE_FORMAT`, `SQLITE_FULL`, `SQLITE_FUNCTION`, `SQLITE_IGNORE`, `SQLITE_INSERT`, `SQLITE_INTERNAL`, `SQLITE_INTERRUPT`, `SQLITE_IOERR`, `SQLITE_IOERR_ACCESS`, `SQLITE_IOERR_AUTH`, `SQLITE_IOERR_BEGIN_ATOMIC`, `SQLITE_IOERR_BLOCKED`, `SQLITE_IOERR_CHECKRESERVEDLOCK`, `SQLITE_IOERR_CLOSE`, `SQLITE_IOERR_COMMIT_ATOMIC`, `SQLITE_IOERR_CONVPATH`, `SQLITE_IOERR_DELETE`, `SQLITE_IOERR_DELETE_NOENT`, `SQLITE_IOERR_DIR_CLOSE`, `SQLITE_IOERR_DIR_FSYNC`, `SQLITE_IOERR_FSTAT`, `SQLITE_IOERR_FSYNC`, `SQLITE_IOERR_GETTEMPPATH`, `SQLITE_IOERR_LOCK`, `SQLITE_IOERR_MMAP`, `SQLITE_IOERR_NOMEM`, `SQLITE_IOERR_RDLOCK`, `SQLITE_IOERR_READ`, `SQLITE_IOERR_ROLLBACK_ATOMIC`, `SQLITE_IOERR_SEEK`, `SQLITE_IOERR_SHMLOCK`, `SQLITE_IOERR_SHMMAP`, `SQLITE_IOERR_SHMOPEN`, `SQLITE_IOERR_SHMSIZE`, `SQLITE_IOERR_SHORT_READ`, `SQLITE_IOERR_TRUNCATE`, `SQLITE_IOERR_UNLOCK`, `SQLITE_IOERR_VNODE`, `SQLITE_IOERR_WRITE`, `SQLITE_LIMIT_ATTACHED`, `SQLITE_LIMIT_COLUMN`, `SQLITE_LIMIT_COMPOUND_SELECT`, `SQLITE_LIMIT_EXPR_DEPTH`, `SQLITE_LIMIT_FUNCTION_ARG`, `SQLITE_LIMIT_LENGTH`, `SQLITE_LIMIT_LIKE_PATTERN_LENGTH`, `SQLITE_LIMIT_SQL_LENGTH`, `SQLITE_LIMIT_TRIGGER_DEPTH`, `SQLITE_LIMIT_VARIABLE_NUMBER`, `SQLITE_LIMIT_VDBE_OP`, `SQLITE_LIMIT_WORKER_THREADS`, `SQLITE_LOCKED`, `SQLITE_LOCKED_SHAREDCACHE`, `SQLITE_LOCKED_VTAB`, `SQLITE_MISMATCH`, `SQLITE_MISUSE`, `SQLITE_NOLFS`, `SQLITE_NOMEM`, `SQLITE_NOTADB`, `SQLITE_NOTFOUND`, `SQLITE_NOTICE`, `SQLITE_NOTICE_RECOVER_ROLLBACK`, `SQLITE_NOTICE_RECOVER_WAL`, `SQLITE_OK`, `SQLITE_OK_LOAD_PERMANENTLY`, `SQLITE_PERM`, `SQLITE_PRAGMA`, `SQLITE_PROTOCOL`, `SQLITE_RANGE`, `SQLITE_READ`, `SQLITE_READONLY`, `SQLITE_READONLY_CANTINIT`, `SQLITE_READONLY_CANTLOCK`, `SQLITE_READONLY_DBMOVED`, `SQLITE_READONLY_DIRECTORY`, `SQLITE_READONLY_RECOVERY`, `SQLITE_READONLY_ROLLBACK`, `SQLITE_RECURSIVE`, `SQLITE_REINDEX`, `SQLITE_ROW`, `SQLITE_SAVEPOINT`, `SQLITE_SCHEMA`, `SQLITE_SELECT`, `SQLITE_TOOBIG`, `SQLITE_TRANSACTION`, `SQLITE_UPDATE`, `SQLITE_WARNING`, `SQLITE_WARNING_AUTOINDEX`, `adapters`, `apilevel`, `converters`, `paramstyle`, `sqlite_version`, `sqlite_version_info`, `threadsafety`.

### sqlite3.dump

_подмодуль · импорт 30 мс_

### ssl

_Python · импорт 164 мс_

> This module provides some more Pythonic support for SSL.

Функции (10):

- `ssl.DER_cert_to_PEM_cert(der_cert_bytes)`
- `ssl.PEM_cert_to_DER_cert(pem_cert_string)`
- `ssl.RAND_add(string, entropy, /)`
- `ssl.RAND_bytes(n, /)`
- `ssl.RAND_status()`
- `ssl.cert_time_to_seconds(cert_time)`
- `ssl.create_default_context(purpose=<Purpose.SERVER_AUTH: _ASN1Object(nid=129, shortname='serverAuth', longname='TLS Web Server Authentication', oid='1.3.6.1.5.5.7.3.1')>, *, cafile=None, capath=None, cadata=None)`
- `ssl.get_default_verify_paths()`
- `ssl.get_protocol_name(protocol_code)`
- `ssl.get_server_certificate(addr, ssl_version=<_SSLMethod.PROTOCOL_TLS_CLIENT: 16>, ca_certs=None, timeout=<object object>)`

Классы (13):

- `ssl.AlertDescription(*values)` — ALERT_DESCRIPTION_ACCESS_DENIED, ALERT_DESCRIPTION_BAD_CERTIFICATE, ALERT_DESCRIPTION_BAD_CERTIFICATE_HASH_VALUE, ALERT_DESCRIPTION_BAD_CERTIFICATE_STATUS_RESPONSE, ALERT_DESCRIPTION_BAD_RECORD_MAC, ALERT_DESCRIPTION_CERTIFICATE_EXPIRED, ALERT_DESCRIPTION_CERTIFICATE_REVOKED, ALERT_DESCRIPTION_CERTIFICATE_UNKNOWN, ALERT_DESCRIPTION_CERTIFICATE_UNOBTAINABLE, ALERT_DESCRIPTION_CLOSE_NOTIFY, ALERT_DESCRIPTION_DECODE_ERROR, ALERT_DESCRIPTION_DECOMPRESSION_FAILURE, ALERT_DESCRIPTION_DECRYPT_ERROR, ALERT_DESCRIPTION_HANDSHAKE_FAILURE, ALERT_DESCRIPTION_ILLEGAL_PARAMETER, ALERT_DESCRIPTION_INSUFFICIENT_SECURITY, ALERT_DESCRIPTION_INTERNAL_ERROR, ALERT_DESCRIPTION_NO_RENEGOTIATION, ALERT_DESCRIPTION_PROTOCOL_VERSION, ALERT_DESCRIPTION_RECORD_OVERFLOW, ALERT_DESCRIPTION_UNEXPECTED_MESSAGE, ALERT_DESCRIPTION_UNKNOWN_CA, ALERT_DESCRIPTION_UNKNOWN_PSK_IDENTITY, ALERT_DESCRIPTION_UNRECOGNIZED_NAME, ALERT_DESCRIPTION_UNSUPPORTED_CERTIFICATE, ALERT_DESCRIPTION_UNSUPPORTED_EXTENSION, ALERT_DESCRIPTION_USER_CANCELLED, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `ssl.DefaultVerifyPaths(cafile, capath, openssl_cafile_env, openssl_cafile, openssl_capath_env, openssl_capath)` — cafile, capath, count, index, openssl_cafile, openssl_cafile_env, openssl_capath, openssl_capath_env
- `ssl.MemoryBIO(…)` — eof, pending, read, write, write_eof
- `ssl.Options(*values)` — OP_CIPHER_SERVER_PREFERENCE, OP_ENABLE_MIDDLEBOX_COMPAT, OP_LEGACY_SERVER_CONNECT, OP_NO_COMPRESSION, OP_NO_RENEGOTIATION, OP_NO_SSLv3, OP_NO_TICKET, OP_NO_TLSv1, OP_NO_TLSv1_1, OP_NO_TLSv1_2, OP_NO_TLSv1_3, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `ssl.Purpose(*values)` — CLIENT_AUTH, SERVER_AUTH, count, fromname, fromnid, index, longname, nid, oid, shortname
- `ssl.SSLContext(protocol=None, *args, **kwargs)` — cert_store_stats, check_hostname, get_ca_certs, get_ciphers, hostname_checks_common_name, keylog_filename, load_cert_chain, load_default_certs, load_dh_params, load_verify_locations, maximum_version, minimum_version, num_tickets, options, post_handshake_auth, protocol, security_level, session_stats, set_alpn_protocols, set_ciphers, set_default_verify_paths, set_ecdh_curve, set_npn_protocols, set_psk_client_callback, set_psk_server_callback, set_servername_callback, sni_callback, sslobject_class, sslsocket_class, verify_flags, verify_mode, wrap_bio, wrap_socket
- `ssl.SSLErrorNumber(*values)` — SSL_ERROR_EOF, SSL_ERROR_INVALID_ERROR_CODE, SSL_ERROR_SSL, SSL_ERROR_SYSCALL, SSL_ERROR_WANT_CONNECT, SSL_ERROR_WANT_READ, SSL_ERROR_WANT_WRITE, SSL_ERROR_WANT_X509_LOOKUP, SSL_ERROR_ZERO_RETURN, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `ssl.SSLObject(*args, **kwargs)` — cipher, compression, context, do_handshake, get_channel_binding, get_unverified_chain, get_verified_chain, getpeercert, pending, read, selected_alpn_protocol, selected_npn_protocol, server_hostname, server_side, session, session_reused, shared_ciphers, unwrap, verify_client_post_handshake, version, write
- `ssl.SSLSession()` — has_ticket, id, ticket_lifetime_hint, time, timeout
- `ssl.SSLSocket(*args, **kwargs)` — accept, bind, cipher, close, compression, connect, connect_ex, context, detach, do_handshake, dup, family, fileno, get_channel_binding, get_inheritable, get_unverified_chain, get_verified_chain, getblocking, getpeercert, getpeername, getsockname, getsockopt, gettimeout, listen, makefile, pending, proto, read, recv, recv_into, recvfrom, recvfrom_into, recvmsg, recvmsg_into, selected_alpn_protocol, selected_npn_protocol, send, sendall, sendfile, sendmsg, sendmsg_afalg, sendto, session, session_reused, set_inheritable, setblocking, setsockopt, settimeout, shared_ciphers, shutdown, timeout, type, unwrap, verify_client_post_handshake, version, write
- `ssl.TLSVersion(*values)` — MAXIMUM_SUPPORTED, MINIMUM_SUPPORTED, SSLv3, TLSv1, TLSv1_1, TLSv1_2, TLSv1_3, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `ssl.VerifyFlags(*values)` — VERIFY_ALLOW_PROXY_CERTS, VERIFY_CRL_CHECK_LEAF, VERIFY_X509_PARTIAL_CHAIN, VERIFY_X509_STRICT, VERIFY_X509_TRUSTED_FIRST, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `ssl.VerifyMode(*values)` — CERT_NONE, CERT_OPTIONAL, CERT_REQUIRED, as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes

Исключения: `ssl.CertificateError`, `ssl.SSLCertVerificationError`, `ssl.SSLEOFError`, `ssl.SSLError`, `ssl.SSLSyscallError`, `ssl.SSLWantReadError`, `ssl.SSLWantWriteError`, `ssl.SSLZeroReturnError`, `ssl.socket_error`.

Константы и данные (89): `ALERT_DESCRIPTION_ACCESS_DENIED`, `ALERT_DESCRIPTION_BAD_CERTIFICATE`, `ALERT_DESCRIPTION_BAD_CERTIFICATE_HASH_VALUE`, `ALERT_DESCRIPTION_BAD_CERTIFICATE_STATUS_RESPONSE`, `ALERT_DESCRIPTION_BAD_RECORD_MAC`, `ALERT_DESCRIPTION_CERTIFICATE_EXPIRED`, `ALERT_DESCRIPTION_CERTIFICATE_REVOKED`, `ALERT_DESCRIPTION_CERTIFICATE_UNKNOWN`, `ALERT_DESCRIPTION_CERTIFICATE_UNOBTAINABLE`, `ALERT_DESCRIPTION_CLOSE_NOTIFY`, `ALERT_DESCRIPTION_DECODE_ERROR`, `ALERT_DESCRIPTION_DECOMPRESSION_FAILURE`, `ALERT_DESCRIPTION_DECRYPT_ERROR`, `ALERT_DESCRIPTION_HANDSHAKE_FAILURE`, `ALERT_DESCRIPTION_ILLEGAL_PARAMETER`, `ALERT_DESCRIPTION_INSUFFICIENT_SECURITY`, `ALERT_DESCRIPTION_INTERNAL_ERROR`, `ALERT_DESCRIPTION_NO_RENEGOTIATION`, `ALERT_DESCRIPTION_PROTOCOL_VERSION`, `ALERT_DESCRIPTION_RECORD_OVERFLOW`, `ALERT_DESCRIPTION_UNEXPECTED_MESSAGE`, `ALERT_DESCRIPTION_UNKNOWN_CA`, `ALERT_DESCRIPTION_UNKNOWN_PSK_IDENTITY`, `ALERT_DESCRIPTION_UNRECOGNIZED_NAME`, `ALERT_DESCRIPTION_UNSUPPORTED_CERTIFICATE`, `ALERT_DESCRIPTION_UNSUPPORTED_EXTENSION`, `ALERT_DESCRIPTION_USER_CANCELLED`, `CERT_NONE`, `CERT_OPTIONAL`, `CERT_REQUIRED`, `CHANNEL_BINDING_TYPES`, `HAS_ALPN`, `HAS_ECDH`, `HAS_NEVER_CHECK_COMMON_NAME`, `HAS_NPN`, `HAS_PSK`, `HAS_SNI`, `HAS_SSLv2`, `HAS_SSLv3`, `HAS_TLSv1`, `HAS_TLSv1_1`, `HAS_TLSv1_2`, `HAS_TLSv1_3`, `OPENSSL_VERSION`, `OPENSSL_VERSION_INFO`, `OPENSSL_VERSION_NUMBER`, `OP_ALL`, `OP_CIPHER_SERVER_PREFERENCE`, `OP_ENABLE_MIDDLEBOX_COMPAT`, `OP_LEGACY_SERVER_CONNECT`, `OP_NO_COMPRESSION`, `OP_NO_RENEGOTIATION`, `OP_NO_SSLv2`, `OP_NO_SSLv3`, `OP_NO_TICKET`, `OP_NO_TLSv1`, `OP_NO_TLSv1_1`, `OP_NO_TLSv1_2`, `OP_NO_TLSv1_3`, `OP_SINGLE_DH_USE`, `OP_SINGLE_ECDH_USE`, `PEM_FOOTER`, `PEM_HEADER`, `PROTOCOL_SSLv23`, `PROTOCOL_TLS`, `PROTOCOL_TLS_CLIENT`, `PROTOCOL_TLS_SERVER`, `PROTOCOL_TLSv1`, `PROTOCOL_TLSv1_1`, `PROTOCOL_TLSv1_2`, `SOCK_STREAM`, `SOL_SOCKET`, `SO_TYPE`, `SSL_ERROR_EOF`, `SSL_ERROR_INVALID_ERROR_CODE`, `SSL_ERROR_SSL`, `SSL_ERROR_SYSCALL`, `SSL_ERROR_WANT_CONNECT`, `SSL_ERROR_WANT_READ`, `SSL_ERROR_WANT_WRITE`, `SSL_ERROR_WANT_X509_LOOKUP`, `SSL_ERROR_ZERO_RETURN`, `VERIFY_ALLOW_PROXY_CERTS`, `VERIFY_CRL_CHECK_CHAIN`, `VERIFY_CRL_CHECK_LEAF`, `VERIFY_DEFAULT`, `VERIFY_X509_PARTIAL_CHAIN`, `VERIFY_X509_STRICT`, `VERIFY_X509_TRUSTED_FIRST`.

### statistics

_Python · импорт 92 мс_

> Basic statistics module.

Функции (20):

- `statistics.correlation(x, y, /, *, method='linear')`
- `statistics.covariance(x, y, /)`
- `statistics.fmean(data, weights=None)`
- `statistics.geometric_mean(data)`
- `statistics.harmonic_mean(data, weights=None)`
- `statistics.kde(data, h, kernel='normal', *, cumulative=False)`
- `statistics.kde_random(data, h, kernel='normal', *, seed=None)`
- `statistics.linear_regression(x, y, /, *, proportional=False)`
- `statistics.mean(data)`
- `statistics.median(data)`
- `statistics.median_grouped(data, interval=1.0)`
- `statistics.median_high(data)`
- `statistics.median_low(data)`
- `statistics.mode(data)`
- `statistics.multimode(data)`
- `statistics.pstdev(data, mu=None)`
- `statistics.pvariance(data, mu=None)`
- `statistics.quantiles(data, *, n=4, method='exclusive')`
- `statistics.stdev(data, xbar=None)`
- `statistics.variance(data, xbar=None)`

Классы (1):

- `statistics.NormalDist(mu=0.0, sigma=1.0)` — cdf, from_samples, inv_cdf, mean, median, mode, overlap, pdf, quantiles, samples, stdev, variance, zscore

Исключения: `statistics.StatisticsError`.

### string

_Python · импорт 12 мс_

> A collection of string constants.

Функции (1):

- `string.capwords(s, sep=None)`

Классы (2):

- `string.Formatter()` — check_unused_args, convert_field, format, format_field, get_field, get_value, parse, vformat
- `string.Template(template)` — braceidpattern, delimiter, flags, get_identifiers, idpattern, is_valid, pattern, safe_substitute, substitute

Константы и данные: `string.ascii_letters` = `'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'`, `string.ascii_lowercase` = `'abcdefghijklmnopqrstuvwxyz'`, `string.ascii_uppercase` = `'ABCDEFGHIJKLMNOPQRSTUVWXYZ'`, `string.digits` = `'0123456789'`, `string.hexdigits` = `'0123456789abcdefABCDEF'`, `string.octdigits` = `'01234567'`, `string.printable` (str), `string.punctuation` = `'!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'`, `string.whitespace` = `' \t\n\r\x0b\x0c'`.

### stringprep

_Python · импорт 12 мс_

> Library that exposes various tables found in the StringPrep RFC 3454.

Функции (19):

- `stringprep.in_table_a1(code)`
- `stringprep.in_table_b1(code)`
- `stringprep.in_table_c11(code)`
- `stringprep.in_table_c11_c12(code)`
- `stringprep.in_table_c12(code)`
- `stringprep.in_table_c21(code)`
- `stringprep.in_table_c21_c22(code)`
- `stringprep.in_table_c22(code)`
- `stringprep.in_table_c3(code)`
- `stringprep.in_table_c4(code)`
- `stringprep.in_table_c5(code)`
- `stringprep.in_table_c6(code)`
- `stringprep.in_table_c7(code)`
- `stringprep.in_table_c8(code)`
- `stringprep.in_table_c9(code)`
- `stringprep.in_table_d1(code)`
- `stringprep.in_table_d2(code)`
- `stringprep.map_table_b2(a)`
- `stringprep.map_table_b3(code)`

Константы и данные: `stringprep.b1_set` (set), `stringprep.b3_exceptions` (dict), `stringprep.c22_specials` (set), `stringprep.c6_set` (set), `stringprep.c7_set` (set), `stringprep.c8_set` (set), `stringprep.c9_set` (set), `stringprep.unicodedata_320` (UCD).

### struct

_Python · импорт 6.2 мс_

> Functions to convert between Python values and C structs.

Функции (6):

- `struct.calcsize(format, /)`
- `struct.iter_unpack(format, buffer, /)`
- `struct.pack(format, v1, v2, ...) -> bytes`
- `struct.pack_into(format, buffer, offset, v1, v2, ...)`
- `struct.unpack(format, buffer, /)`
- `struct.unpack_from(format, /, buffer, offset=0)`

Классы (1):

- `struct.Struct(fmt) --> compiled struct object` — format, iter_unpack, pack, pack_into, size, unpack, unpack_from

Исключения: `struct.error`.

### sys

_встроенный · импорт 0.02 мс_

> This module provides access to some objects used or maintained by the

Функции (38):

- `sys.activate_stack_trampoline(backend, /)`
- `sys.addaudithook(hook)`
- `sys.audit(event, /, *args)`
- `sys.breakpointhook(*args, **kwargs)`
- `sys.call_tracing(func, args, /)`
- `sys.deactivate_stack_trampoline()`
- `sys.displayhook(object, /)`
- `sys.exc_info()`
- `sys.excepthook(exctype, value, traceback, /)`
- `sys.exception()`
- `sys.exit(status=None, /)`
- `sys.get_asyncgen_hooks()`
- `sys.get_coroutine_origin_tracking_depth()`
- `sys.get_int_max_str_digits()`
- `sys.getallocatedblocks()`
- `sys.getdefaultencoding()`
- `sys.getdlopenflags()`
- `sys.getfilesystemencodeerrors()`
- `sys.getfilesystemencoding()`
- `sys.getprofile()`
- `sys.getrecursionlimit()`
- `sys.getrefcount(object, /)`
- `sys.getsizeof(object [, default]) -> int`
- `sys.getswitchinterval()`
- `sys.gettrace()`
- `sys.getunicodeinternedsize(*, _only_immortal=False)`
- `sys.intern(string, /)`
- `sys.is_finalizing()`
- `sys.is_stack_trampoline_active()`
- `sys.set_asyncgen_hooks([firstiter] [, finalizer])`
- `sys.set_coroutine_origin_tracking_depth(depth)`
- `sys.set_int_max_str_digits(maxdigits)`
- `sys.setdlopenflags(flags, /)`
- `sys.setprofile(function, /)`
- `sys.setrecursionlimit(limit, /)`
- `sys.setswitchinterval(interval, /)`
- `sys.settrace(function, /)`
- `sys.unraisablehook(unraisable, /)`

Константы и данные: `sys.abiflags` = `''`, `sys.api_version` = `1013`, `sys.argv` (list), `sys.base_exec_prefix` = `'/usr/local'`, `sys.base_prefix` = `'/usr/local'`, `sys.builtin_module_names` (tuple), `sys.byteorder` = `'little'`, `sys.copyright` (str), `sys.dont_write_bytecode` = `True`, `sys.exec_prefix` = `'/usr/local'`, `sys.executable` = `'/bin/python3'`, `sys.flags` (flags), `sys.float_info` (float_info), `sys.float_repr_style` = `'short'`, `sys.hash_info` (hash_info), `sys.hexversion` = `51187952`, `sys.implementation` (SimpleNamespace), `sys.int_info` (int_info), `sys.maxsize` = `9223372036854775807`, `sys.maxunicode` = `1114111`, `sys.meta_path` (list), `sys.modules` (dict), `sys.orig_argv` (list), `sys.path` (list), `sys.path_hooks` (list), `sys.path_importer_cache` (dict), `sys.platform` = `'linux'`, `sys.platlibdir` = `'lib'`, `sys.prefix` = `'/usr/local'`, `sys.pycache_prefix` (NoneType), `sys.stderr` (TextIOWrapper), `sys.stdin` (TextIOWrapper), `sys.stdlib_module_names` (frozenset), `sys.stdout` (TextIOWrapper), `sys.thread_info` (thread_info), `sys.version` = `'3.13.16 (main, Oct 20 2026, 17:50:09) [GCC 7.5.0]'`, `sys.version_info` (version_info), `sys.warnoptions` (list).

### tarfile

_Python · импорт 102 мс_

> Read from and write to tar format archives.

Функции (5):

- `tarfile.data_filter(member, dest_path)`
- `tarfile.fully_trusted_filter(member, dest_path)`
- `tarfile.is_tarfile(name)`
- `tarfile.open(name=None, mode='r', fileobj=None, bufsize=10240, **kwargs)`
- `tarfile.tar_filter(member, dest_path)`

Классы (2):

- `tarfile.TarFile(name=None, mode='r', fileobj=None, format=None, tarinfo=None, dereference=None, ignore_zeros=None, encoding=None, errors='surrogateescape', pax_headers=None, debug=None, errorlevel=None, copybufsize=None, stream=False)` — OPEN_METH, add, addfile, bz2open, chmod, chown, close, debug, dereference, encoding, errorlevel, errors, extract, extractall, extractfile, extraction_filter, fileobject, format, getmember, getmembers, getnames, gettarinfo, gzopen, ignore_zeros, list, makedev, makedir, makefifo, makefile, makelink, makelink_with_filter, makeunknown, next, open, tarinfo, taropen, utime, xzopen
- `tarfile.TarInfo(name='')` — chksum, create_gnu_header, create_pax_global_header, create_pax_header, create_ustar_header, devmajor, devminor, frombuf, fromtarfile, get_info, gid, gname, isblk, ischr, isdev, isdir, isfifo, isfile, islnk, isreg, issparse, issym, linkname, linkpath, mode, mtime, name, offset, offset_data, path, pax_headers, replace, size, sparse, tarfile, tobuf, type, uid, uname

Исключения: `tarfile.AbsoluteLinkError`, `tarfile.AbsolutePathError`, `tarfile.CompressionError`, `tarfile.ExtractError`, `tarfile.FilterError`, `tarfile.HeaderError`, `tarfile.LinkFallbackError`, `tarfile.LinkOutsideDestinationError`, `tarfile.OutsideDestinationError`, `tarfile.ReadError`, `tarfile.SpecialFileError`, `tarfile.StreamError`, `tarfile.TarError`.

Константы и данные: `tarfile.DEFAULT_FORMAT` = `2`, `tarfile.ENCODING` = `'utf-8'`, `tarfile.GNU_FORMAT` = `1`, `tarfile.PAX_FORMAT` = `2`, `tarfile.USTAR_FORMAT` = `0`.

### tempfile

_Python · импорт 79 мс_

> Temporary files.

Функции (9):

- `tempfile.NamedTemporaryFile(mode='w+b', buffering=-1, encoding=None, newline=None, suffix=None, prefix=None, dir=None, delete=True, *, errors=None, delete_on_close=True)`
- `tempfile.TemporaryFile(mode='w+b', buffering=-1, encoding=None, newline=None, suffix=None, prefix=None, dir=None, *, errors=None)`
- `tempfile.gettempdir()`
- `tempfile.gettempdirb()`
- `tempfile.gettempprefix()`
- `tempfile.gettempprefixb()`
- `tempfile.mkdtemp(suffix=None, prefix=None, dir=None)`
- `tempfile.mkstemp(suffix=None, prefix=None, dir=None, text=False)`
- `tempfile.mktemp(suffix='', prefix='tmp', dir=None)`

Классы (2):

- `tempfile.SpooledTemporaryFile(max_size=0, mode='w+b', buffering=-1, encoding=None, newline=None, suffix=None, prefix=None, dir=None, *, errors=None)` — close, closed, detach, encoding, errors, fileno, flush, isatty, mode, name, newlines, read, read1, readable, readinto, readinto1, readline, readlines, rollover, seek, seekable, tell, truncate, writable, write, writelines
- `tempfile.TemporaryDirectory(suffix=None, prefix=None, dir=None, ignore_cleanup_errors=False, *, delete=True)` — cleanup

Константы и данные: `tempfile.TMP_MAX` = `20`, `tempfile.tempdir` (NoneType).

### termios

_расширение (.so) · импорт 6.5 мс_

> This module provides an interface to the Posix calls for tty I/O control.

Функции (8):

- `termios.tcdrain(fd, /)`
- `termios.tcflow(fd, action, /)`
- `termios.tcflush(fd, queue, /)`
- `termios.tcgetattr(fd, /)`
- `termios.tcgetwinsize(fd, /)`
- `termios.tcsendbreak(fd, duration, /)`
- `termios.tcsetattr(fd, when, attributes, /)`
- `termios.tcsetwinsize(fd, winsize, /)`

Исключения: `termios.error`.

Константы и данные (233): `B0`, `B1000000`, `B110`, `B115200`, `B1152000`, `B1200`, `B134`, `B150`, `B1500000`, `B1800`, `B19200`, `B200`, `B2000000`, `B230400`, `B2400`, `B2500000`, `B300`, `B3000000`, `B3500000`, `B38400`, `B4000000`, `B460800`, `B4800`, `B50`, `B500000`, `B57600`, `B576000`, `B600`, `B75`, `B921600`, `B9600`, `BRKINT`, `BS0`, `BS1`, `BSDLY`, `CBAUD`, `CBAUDEX`, `CDSUSP`, `CEOF`, `CEOL`, `CEOT`, `CERASE`, `CFLUSH`, `CIBAUD`, `CINTR`, `CKILL`, `CLNEXT`, `CLOCAL`, `CQUIT`, `CR0`, `CR1`, `CR2`, `CR3`, `CRDLY`, `CREAD`, `CRPRNT`, `CRTSCTS`, `CS5`, `CS6`, `CS7`, `CS8`, `CSIZE`, `CSTART`, `CSTOP`, `CSTOPB`, `CSUSP`, `CWERASE`, `ECHO`, `ECHOCTL`, `ECHOE`, `ECHOK`, `ECHOKE`, `ECHONL`, `ECHOPRT`, `EXTA`, `EXTB`, `EXTPROC`, `FF0`, `FF1`, `FFDLY`, `FIOASYNC`, `FIOCLEX`, `FIONBIO`, `FIONCLEX`, `FIONREAD`, `FLUSHO`, `HUPCL`, `ICANON`, `ICRNL`, `IEXTEN`, `IGNBRK`, `IGNCR`, `IGNPAR`, `IMAXBEL`, `INLCR`, `INPCK`, `IOCSIZE_MASK`, `IOCSIZE_SHIFT`, `ISIG`, `ISTRIP`, `IUCLC`, `IUTF8`, `IXANY`, `IXOFF`, `IXON`, `NCC`, `NCCS`, `NL0`, `NL1`, `NLDLY`, `NOFLSH`, `N_MOUSE`, `N_PPP`, `N_SLIP`, `N_STRIP`, `N_TTY`, `OCRNL`, `OFDEL`, `OFILL`, `OLCUC`, `ONLCR`, `ONLRET`, `ONOCR`, `OPOST`, `PARENB`, `PARMRK`, `PARODD`, `PENDIN`, `TAB0`, `TAB1`, `TAB2`, `TAB3`, `TABDLY`, `TCFLSH`, `TCGETA`, `TCGETS`, `TCIFLUSH`, `TCIOFF`, `TCIOFLUSH`, `TCION`, `TCOFLUSH`, `TCOOFF`, `TCOON`, `TCSADRAIN`, `TCSAFLUSH`, `TCSANOW`, `TCSBRK`, `TCSBRKP`, `TCSETA`, `TCSETAF`, `TCSETAW`, `TCSETS`, `TCSETSF`, `TCSETSW`, `TCXONC`, `TIOCCONS`, `TIOCEXCL`, `TIOCGETD`, `TIOCGICOUNT`, `TIOCGLCKTRMIOS`, `TIOCGPGRP`, `TIOCGSERIAL`, `TIOCGSOFTCAR`, `TIOCGWINSZ`, `TIOCINQ`, `TIOCLINUX`, `TIOCMBIC`, `TIOCMBIS`, `TIOCMGET`, `TIOCMIWAIT`, `TIOCMSET`, `TIOCM_CAR`, `TIOCM_CD`, `TIOCM_CTS`, `TIOCM_DSR`, `TIOCM_DTR`, `TIOCM_LE`, `TIOCM_RI`, `TIOCM_RNG`, `TIOCM_RTS`, `TIOCM_SR`, `TIOCM_ST`, `TIOCNOTTY`, `TIOCNXCL`, `TIOCOUTQ`, `TIOCPKT`, `TIOCPKT_DATA`, `TIOCPKT_DOSTOP`, `TIOCPKT_FLUSHREAD`, `TIOCPKT_FLUSHWRITE`, `TIOCPKT_NOSTOP`, `TIOCPKT_START`, `TIOCPKT_STOP`, `TIOCSCTTY`, `TIOCSERCONFIG`, `TIOCSERGETLSR`, `TIOCSERGETMULTI`, `TIOCSERGSTRUCT`, `TIOCSERGWILD`, `TIOCSERSETMULTI`, `TIOCSERSWILD`, `TIOCSER_TEMT`, `TIOCSETD`, `TIOCSLCKTRMIOS`, `TIOCSPGRP`, `TIOCSSERIAL`, `TIOCSSOFTCAR`, `TIOCSTI`, `TIOCSWINSZ`, `TOSTOP`, `VDISCARD`, `VEOF`, `VEOL`, `VEOL2`, `VERASE`, `VINTR`, `VKILL`, `VLNEXT`, `VMIN`, `VQUIT`, `VREPRINT`, `VSTART`, `VSTOP`, `VSUSP`, `VSWTC`, `VSWTCH`, `VT0`, `VT1`, `VTDLY`, `VTIME`, `VWERASE`, `XCASE`, `XTABS`.

### textwrap

_Python · импорт 0.02 мс_

> Text wrapping and filling.

Функции (5):

- `textwrap.dedent(text)`
- `textwrap.fill(text, width=70, **kwargs)`
- `textwrap.indent(text, prefix, predicate=None)`
- `textwrap.shorten(text, width, **kwargs)`
- `textwrap.wrap(text, width=70, **kwargs)`

Классы (1):

- `textwrap.TextWrapper(width=70, initial_indent='', subsequent_indent='', expand_tabs=True, replace_whitespace=True, fix_sentence_endings=False, break_long_words=True, drop_whitespace=True, break_on_hyphens=True, tabsize=8, *, max_lines=None, placeholder=' [...…` — fill, sentence_end_re, unicode_whitespace_trans, wordsep_re, wordsep_simple_re, wrap

### time

_встроенный · импорт 0.01 мс_

> This module provides various functions to manipulate time values.

**Время в скрипте:** промежутки считайте по `time.monotonic()` (его не переводят; и внутри вызова, и между вызовами), а не числом вызовов: вызов асинхронный, промежутки между вызовами неравные, после остановки или правки скрипта его переменные начинаются заново. `time.time()` — календарное время, его переводят при установке и синхронизации часов; `time.sleep` в блоке недоступна. Опора времени — часы контроллера или сигнал программы ПЛК (раздел 8.9 основного файла).

Функции (25):

- `time.asctime([time_tuple]) -> string`
- `time.clock_getres(clk_id) -> floating-point number`
- `time.clock_gettime(clk_id, /)`
- `time.clock_gettime_ns(clk_id, /)`
- `time.clock_settime(clk_id, time)`
- `time.clock_settime_ns(clk_id, time)`
- `time.ctime([seconds]) -> string`
- `time.get_clock_info(name: str) -> dict`
- `time.gmtime([seconds]) -> (tm_year, tm_mon, tm_mday, tm_hour, tm_min,`
- `time.localtime([seconds]) -> (tm_year,tm_mon,tm_mday,tm_hour,tm_min,`
- `time.mktime(object, /)`
- `time.monotonic()`
- `time.monotonic_ns()`
- `time.perf_counter()`
- `time.perf_counter_ns()`
- `time.process_time()`
- `time.process_time_ns()`
- `time.pthread_getcpuclockid(thread_id) -> int`
- `time.sleep(object, /)`
- `time.strftime(format[, time_tuple]) -> string`
- `time.strptime(string[, format]) -> struct_time`
- `time.thread_time()`
- `time.thread_time_ns()`
- `time.time()`
- `time.time_ns()`

Классы (1):

- `time.struct_time(iterable=(), /)` — count, index, n_fields, n_sequence_fields, n_unnamed_fields, tm_gmtoff, tm_hour, tm_isdst, tm_mday, tm_min, tm_mon, tm_sec, tm_wday, tm_yday, tm_year, tm_zone

Константы и данные: `time.CLOCK_BOOTTIME` = `7`, `time.CLOCK_MONOTONIC` = `1`, `time.CLOCK_MONOTONIC_RAW` = `4`, `time.CLOCK_PROCESS_CPUTIME_ID` = `2`, `time.CLOCK_REALTIME` = `0`, `time.CLOCK_TAI` = `11`, `time.CLOCK_THREAD_CPUTIME_ID` = `3`, `time.altzone` = `0`, `time.daylight` = `0`, `time.timezone` = `0`, `time.tzname` (tuple).

### tomllib

_пакет · импорт 156 мс_

Функции (2):

- `tomllib.load(fp: 'BinaryIO', /, *, parse_float: 'ParseFloat' = <class 'float'>) -> 'dict[str, Any]'`
- `tomllib.loads(s: 'str', /, *, parse_float: 'ParseFloat' = <class 'float'>) -> 'dict[str, Any]'`

Исключения: `tomllib.TOMLDecodeError`.

### traceback

_Python · импорт 0.02 мс_

> Extract, format and print information about Python stack traces.

Функции (16):

- `traceback.clear_frames(tb)`
- `traceback.extract_stack(f=None, limit=None)`
- `traceback.extract_tb(tb, limit=None)`
- `traceback.format_exc(limit=None, chain=True)`
- `traceback.format_exception(exc, /, value=<implicit>, tb=<implicit>, limit=None, chain=True, **kwargs)`
- `traceback.format_exception_only(exc, /, value=<implicit>, *, show_group=False, **kwargs)`
- `traceback.format_list(extracted_list)`
- `traceback.format_stack(f=None, limit=None)`
- `traceback.format_tb(tb, limit=None)`
- `traceback.print_exc(limit=None, file=None, chain=True)`
- `traceback.print_exception(exc, /, value=<implicit>, tb=<implicit>, limit=None, file=None, chain=True, **kwargs)`
- `traceback.print_last(limit=None, file=None, chain=True)`
- `traceback.print_stack(f=None, limit=None, file=None)`
- `traceback.print_tb(tb, limit=None, file=None)`
- `traceback.walk_stack(f)`
- `traceback.walk_tb(tb)`

Классы (3):

- `traceback.FrameSummary(filename, lineno, name, *, lookup_line=True, locals=None, line=None, end_lineno=None, colno=None, end_colno=None, **kwargs)` — colno, end_colno, end_lineno, filename, line, lineno, locals, name
- `traceback.StackSummary(iterable=(), /)` — append, clear, copy, count, extend, extract, format, format_frame_summary, from_list, index, insert, pop, remove, reverse, sort
- `traceback.TracebackException(exc_type, exc_value, exc_traceback, *, limit=None, lookup_lines=True, capture_locals=False, compact=False, max_group_width=15, max_group_depth=10, save_exc_type=True, _seen=None)` — exc_type, exc_type_str, format, format_exception_only, from_exception, print

### tty

_Python · импорт 9.7 мс_

> Terminal utilities.

Функции (4):

- `tty.cfmakecbreak(mode)`
- `tty.cfmakeraw(mode)`
- `tty.setcbreak(fd, when=2)`
- `tty.setraw(fd, when=2)`

### types

_Python · импорт 0.02 мс_

> Define names for built-in types that aren't directly accessible as a builtin.

Функции (5):

- `types.coroutine(func)`
- `types.get_original_bases(cls, /)`
- `types.new_class(name, bases=(), kwds=None, exec_body=None)`
- `types.prepare_class(name, bases=(), kwds=None)`
- `types.resolve_bases(bases)`

Классы (28):

- `types.AsyncGeneratorType()` — aclose, ag_await, ag_code, ag_frame, ag_running, ag_suspended, asend, athrow
- `types.BuiltinFunctionType()`
- `types.BuiltinMethodType()`
- `types.CapsuleType()`
- `types.CellType(…)` — cell_contents
- `types.ClassMethodDescriptorType()`
- `types.CodeType(argcount, posonlyargcount, kwonlyargcount, nlocals, stacksize, flags, codestring, constants, names, varnames, filename, name, qualname, firstlineno, linetable, exceptiontable, freevars=(), cellvars=(), /)` — co_argcount, co_cellvars, co_code, co_consts, co_exceptiontable, co_filename, co_firstlineno, co_flags, co_freevars, co_kwonlyargcount, co_lines, co_linetable, co_lnotab, co_name, co_names, co_nlocals, co_positions, co_posonlyargcount, co_qualname, co_stacksize, co_varnames, replace
- `types.CoroutineType()` — close, cr_await, cr_code, cr_frame, cr_origin, cr_running, cr_suspended, send, throw
- `types.DynamicClassAttribute(fget=None, fset=None, fdel=None, doc=None)` — deleter, getter, setter
- `types.EllipsisType()`
- `types.FrameType()` — clear, f_back, f_builtins, f_code, f_globals, f_lasti, f_lineno, f_locals, f_trace, f_trace_lines, f_trace_opcodes
- `types.FunctionType(code, globals, name=None, argdefs=None, closure=None, kwdefaults=None)`
- `types.GeneratorType()` — close, gi_code, gi_frame, gi_running, gi_suspended, gi_yieldfrom, send, throw
- `types.GenericAlias(origin, args, /)`
- `types.GetSetDescriptorType()`
- `types.LambdaType(code, globals, name=None, argdefs=None, closure=None, kwdefaults=None)`
- `types.MappingProxyType(mapping)` — copy, get, items, keys, values
- `types.MemberDescriptorType()`
- `types.MethodDescriptorType()`
- `types.MethodType(function, instance, /)`
- `types.MethodWrapperType()`
- `types.ModuleType(name, doc=None)`
- `types.NoneType()`
- `types.NotImplementedType()`
- `types.SimpleNamespace(mapping_or_iterable=(), /, **kwargs)`
- `types.TracebackType(tb_next, tb_frame, tb_lasti, tb_lineno)` — tb_frame, tb_lasti, tb_lineno, tb_next
- `types.UnionType()`
- `types.WrapperDescriptorType()`

### typing

_Python · импорт 61 мс_

> The typing module: Support for gradual typing as defined by PEP 484 and subsequent PEPs.

Функции (79):

- `typing.AbstractSet(*args, **kwargs)`
- `typing.Annotated(*args, **kwds)`
- `typing.AsyncContextManager(*args, **kwargs)`
- `typing.AsyncGenerator(*args, **kwargs)`
- `typing.AsyncIterable(*args, **kwargs)`
- `typing.AsyncIterator(*args, **kwargs)`
- `typing.Awaitable(*args, **kwargs)`
- `typing.ByteString(*args, **kwargs)`
- `typing.Callable(*args, **kwargs)`
- `typing.ChainMap(*args, **kwargs)`
- `typing.ClassVar(*args, **kwds)`
- `typing.Collection(*args, **kwargs)`
- `typing.Concatenate(*args, **kwds)`
- `typing.Container(*args, **kwargs)`
- `typing.ContextManager(*args, **kwargs)`
- `typing.Coroutine(*args, **kwargs)`
- `typing.Counter(*args, **kwargs)`
- `typing.DefaultDict(*args, **kwargs)`
- `typing.Deque(*args, **kwargs)`
- `typing.Dict(*args, **kwargs)`
- `typing.Final(*args, **kwds)`
- `typing.FrozenSet(*args, **kwargs)`
- `typing.Generator(*args, **kwargs)`
- `typing.Hashable(*args, **kwargs)`
- `typing.ItemsView(*args, **kwargs)`
- `typing.Iterable(*args, **kwargs)`
- `typing.Iterator(*args, **kwargs)`
- `typing.KeysView(*args, **kwargs)`
- `typing.List(*args, **kwargs)`
- `typing.Literal(*args, **kwds)`
- `typing.LiteralString(*args, **kwds)`
- `typing.Mapping(*args, **kwargs)`
- `typing.MappingView(*args, **kwargs)`
- `typing.Match(*args, **kwargs)`
- `typing.MutableMapping(*args, **kwargs)`
- `typing.MutableSequence(*args, **kwargs)`
- `typing.MutableSet(*args, **kwargs)`
- `typing.NamedTuple(typename, fields=<sentinel>, /, **kwargs)`
- `typing.Never(*args, **kwds)`
- `typing.NoReturn(*args, **kwds)`
- `typing.NotRequired(*args, **kwds)`
- `typing.Optional(*args, **kwds)`
- `typing.OrderedDict(*args, **kwargs)`
- `typing.Pattern(*args, **kwargs)`
- `typing.ReadOnly(*args, **kwds)`
- `typing.Required(*args, **kwds)`
- `typing.Reversible(*args, **kwargs)`
- `typing.Self(*args, **kwds)`
- `typing.Sequence(*args, **kwargs)`
- `typing.Set(*args, **kwargs)`
- `typing.Sized(*args, **kwargs)`
- `typing.Tuple(*args, **kwargs)`
- `typing.Type(*args, **kwargs)`
- `typing.TypeAlias(*args, **kwds)`
- `typing.TypeGuard(*args, **kwds)`
- `typing.TypeIs(*args, **kwds)`
- `typing.TypedDict(typename, fields=<sentinel>, /, *, total=True)`
- `typing.Union(*args, **kwds)`
- `typing.Unpack(*args, **kwds)`
- `typing.ValuesView(*args, **kwargs)`
- `typing.assert_never(arg: Never, /) -> Never`
- `typing.assert_type(val, typ, /)`
- `typing.cast(typ, val)`
- `typing.clear_overloads()`
- `typing.dataclass_transform(*, eq_default: bool = True, order_default: bool = False, kw_only_default: bool = False, frozen_default: bool = False, field_specifiers: tuple[typing.Union[type[typing.Any], typing.Callable[..., typing.Any]], ...] = (), **kwargs: Any) -> <…`
- `typing.final(f)`
- `typing.get_args(tp)`
- `typing.get_origin(tp)`
- `typing.get_overloads(func)`
- `typing.get_protocol_members(tp: type, /) -> frozenset[str]`
- `typing.get_type_hints(obj, globalns=None, localns=None, include_extras=False)`
- `typing.is_protocol(tp: type, /) -> bool`
- `typing.is_typeddict(tp)`
- `typing.no_type_check(arg)`
- `typing.no_type_check_decorator(decorator)`
- `typing.overload(func)`
- `typing.override(method: F, /) -> F`
- `typing.reveal_type(obj: T, /) -> T`
- `typing.runtime_checkable(cls)`

Классы (22):

- `typing.Any(*args, **kwargs)`
- `typing.BinaryIO()` — close, closed, fileno, flush, isatty, mode, name, read, readable, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `typing.ForwardRef(arg, is_argument=True, module=None, *, is_class=False)`
- `typing.Generic()`
- `typing.IO()` — close, closed, fileno, flush, isatty, mode, name, read, readable, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `typing.NewType(name, tp)`
- `typing.ParamSpec(…)` — args, has_default, kwargs
- `typing.ParamSpecArgs(…)`
- `typing.ParamSpecKwargs(…)`
- `typing.Protocol()`
- `typing.SupportsAbs(*args, **kwargs)`
- `typing.SupportsBytes(*args, **kwargs)`
- `typing.SupportsComplex(*args, **kwargs)`
- `typing.SupportsFloat(*args, **kwargs)`
- `typing.SupportsIndex(*args, **kwargs)`
- `typing.SupportsInt(*args, **kwargs)`
- `typing.SupportsRound(*args, **kwargs)`
- `typing.Text(object='') -> str` — capitalize, casefold, center, count, encode, endswith, expandtabs, find, format, format_map, index, isalnum, isalpha, isascii, isdecimal, isdigit, isidentifier, islower, isnumeric, isprintable, isspace, istitle, isupper, join, ljust, lower, lstrip, maketrans, partition, removeprefix, removesuffix, replace, rfind, rindex, rjust, rpartition, rsplit, rstrip, split, splitlines, startswith, strip, swapcase, title, translate, upper, zfill
- `typing.TextIO()` — buffer, close, closed, encoding, errors, fileno, flush, isatty, line_buffering, mode, name, newlines, read, readable, readline, readlines, seek, seekable, tell, truncate, writable, write, writelines
- `typing.TypeAliasType(…)`
- `typing.TypeVar(…)` — has_default
- `typing.TypeVarTuple(…)` — has_default

Константы и данные: `typing.AnyStr` (TypeVar), `typing.NoDefault` (NoDefaultType), `typing.TYPE_CHECKING` = `False`.

### unicodedata

_расширение (.so) · импорт 2.9 мс_

> This module provides access to the Unicode Character Database which

Функции (13):

- `unicodedata.bidirectional(chr, /)`
- `unicodedata.category(chr, /)`
- `unicodedata.combining(chr, /)`
- `unicodedata.decimal(…)`
- `unicodedata.decomposition(chr, /)`
- `unicodedata.digit(…)`
- `unicodedata.east_asian_width(chr, /)`
- `unicodedata.is_normalized(form, unistr, /)`
- `unicodedata.lookup(name, /)`
- `unicodedata.mirrored(chr, /)`
- `unicodedata.name(…)`
- `unicodedata.normalize(form, unistr, /)`
- `unicodedata.numeric(…)`

Классы (1):

- `unicodedata.UCD()` — bidirectional, category, combining, decimal, decomposition, digit, east_asian_width, is_normalized, lookup, mirrored, name, normalize, numeric, unidata_version

Константы и данные: `unicodedata.ucd_3_2_0` (UCD), `unicodedata.unidata_version` = `'15.1.0'`.

### urllib

_пакет · импорт 2.7 мс_

Подмодули: `urllib.error`, `urllib.parse`, `urllib.request`, `urllib.response`, `urllib.robotparser`.

### urllib.error

_подмодуль · импорт 107 мс_

> Exception classes raised by urllib.

Исключения: `urllib.error.ContentTooShortError`, `urllib.error.HTTPError`, `urllib.error.URLError`.

### urllib.parse

_подмодуль · импорт 59 мс_

> Parse (absolute and relative) URLs.

Функции (15):

- `urllib.parse.parse_qs(qs, keep_blank_values=False, strict_parsing=False, encoding='utf-8', errors='replace', max_num_fields=None, separator='&')`
- `urllib.parse.parse_qsl(qs, keep_blank_values=False, strict_parsing=False, encoding='utf-8', errors='replace', max_num_fields=None, separator='&')`
- `urllib.parse.quote(string, safe='/', encoding=None, errors=None)`
- `urllib.parse.quote_from_bytes(bs, safe='/')`
- `urllib.parse.quote_plus(string, safe='', encoding=None, errors=None)`
- `urllib.parse.unquote(string, encoding='utf-8', errors='replace')`
- `urllib.parse.unquote_plus(string, encoding='utf-8', errors='replace')`
- `urllib.parse.unquote_to_bytes(string)`
- `urllib.parse.urldefrag(url)`
- `urllib.parse.urlencode(query, doseq=False, safe='', encoding=None, errors=None, quote_via=<function quote_plus>)`
- `urllib.parse.urljoin(base, url, allow_fragments=True)`
- `urllib.parse.urlparse(url, scheme='', allow_fragments=True)`
- `urllib.parse.urlsplit(url, scheme='', allow_fragments=True)`
- `urllib.parse.urlunparse(components)`
- `urllib.parse.urlunsplit(components)`

Классы (6):

- `urllib.parse.DefragResult(url, fragment)` — count, encode, fragment, geturl, index, url
- `urllib.parse.DefragResultBytes(url, fragment)` — count, decode, fragment, geturl, index, url
- `urllib.parse.ParseResult(scheme, netloc, path, params, query, fragment)` — count, encode, fragment, geturl, hostname, index, netloc, params, password, path, port, query, scheme, username
- `urllib.parse.ParseResultBytes(scheme, netloc, path, params, query, fragment)` — count, decode, fragment, geturl, hostname, index, netloc, params, password, path, port, query, scheme, username
- `urllib.parse.SplitResult(scheme, netloc, path, query, fragment)` — count, encode, fragment, geturl, hostname, index, netloc, password, path, port, query, scheme, username
- `urllib.parse.SplitResultBytes(scheme, netloc, path, query, fragment)` — count, decode, fragment, geturl, hostname, index, netloc, password, path, port, query, scheme, username

### urllib.request

_подмодуль · импорт 554 мс_

> An extensible library for opening URLs using a variety of protocols

Функции (8):

- `urllib.request.build_opener(*handlers)`
- `urllib.request.getproxies()`
- `urllib.request.install_opener(opener)`
- `urllib.request.pathname2url(pathname)`
- `urllib.request.url2pathname(pathname)`
- `urllib.request.urlcleanup()`
- `urllib.request.urlopen(url, data=None, timeout=<object object>, *, context=None)`
- `urllib.request.urlretrieve(url, filename=None, reporthook=None, data=None)`

Классы (26):

- `urllib.request.AbstractBasicAuthHandler(password_mgr=None)` — http_error_auth_reqed, http_request, http_response, https_request, https_response, retry_http_basic_auth, rx
- `urllib.request.AbstractDigestAuthHandler(passwd=None)` — get_algorithm_impls, get_authorization, get_cnonce, get_entity_digest, http_error_auth_reqed, reset_retry_count, retry_http_digest_auth
- `urllib.request.BaseHandler()` — add_parent, close, handler_order
- `urllib.request.CacheFTPHandler()` — add_parent, check_cache, clear_cache, close, connect_ftp, ftp_open, handler_order, setMaxConns, setTimeout
- `urllib.request.DataHandler()` — add_parent, close, data_open, handler_order
- `urllib.request.FTPHandler()` — add_parent, close, connect_ftp, ftp_open, handler_order
- `urllib.request.FancyURLopener(*args, **kwargs)` — addheader, cleanup, close, get_user_passwd, http_error, http_error_301, http_error_302, http_error_303, http_error_307, http_error_308, http_error_401, http_error_407, http_error_default, open, open_data, open_file, open_ftp, open_http, open_https, open_local_file, open_unknown, open_unknown_proxy, prompt_user_passwd, redirect_internal, retrieve, retry_http_basic_auth, retry_https_basic_auth, retry_proxy_http_basic_auth, retry_proxy_https_basic_auth, version
- `urllib.request.FileHandler()` — add_parent, close, file_open, get_names, handler_order, names, open_local_file
- `urllib.request.HTTPBasicAuthHandler(password_mgr=None)` — add_parent, auth_header, close, handler_order, http_error_401, http_error_auth_reqed, http_request, http_response, https_request, https_response, retry_http_basic_auth, rx
- `urllib.request.HTTPCookieProcessor(cookiejar=None)` — add_parent, close, handler_order, http_request, http_response, https_request, https_response
- `urllib.request.HTTPDefaultErrorHandler()` — add_parent, close, handler_order, http_error_default
- `urllib.request.HTTPDigestAuthHandler(passwd=None)` — add_parent, auth_header, close, get_algorithm_impls, get_authorization, get_cnonce, get_entity_digest, handler_order, http_error_401, http_error_auth_reqed, reset_retry_count, retry_http_digest_auth
- `urllib.request.HTTPErrorProcessor()` — add_parent, close, handler_order, http_response, https_response
- `urllib.request.HTTPHandler(debuglevel=None)` — add_parent, close, do_open, do_request_, handler_order, http_open, http_request, set_http_debuglevel
- `urllib.request.HTTPPasswordMgr()` — add_password, find_user_password, is_suburi, reduce_uri
- `urllib.request.HTTPPasswordMgrWithDefaultRealm()` — add_password, find_user_password, is_suburi, reduce_uri
- `urllib.request.HTTPPasswordMgrWithPriorAuth()` — add_password, find_user_password, is_authenticated, is_suburi, reduce_uri, update_authenticated
- `urllib.request.HTTPRedirectHandler()` — add_parent, close, handler_order, http_error_301, http_error_302, http_error_303, http_error_307, http_error_308, inf_msg, max_redirections, max_repeats, redirect_request
- `urllib.request.HTTPSHandler(debuglevel=None, context=None, check_hostname=None)` — add_parent, close, do_open, do_request_, handler_order, https_open, https_request, set_http_debuglevel
- `urllib.request.OpenerDirector()` — add_handler, close, error, open
- `urllib.request.ProxyBasicAuthHandler(password_mgr=None)` — add_parent, auth_header, close, handler_order, http_error_407, http_error_auth_reqed, http_request, http_response, https_request, https_response, retry_http_basic_auth, rx
- `urllib.request.ProxyDigestAuthHandler(passwd=None)` — add_parent, auth_header, close, get_algorithm_impls, get_authorization, get_cnonce, get_entity_digest, handler_order, http_error_407, http_error_auth_reqed, reset_retry_count, retry_http_digest_auth
- `urllib.request.ProxyHandler(proxies=None)` — add_parent, close, handler_order, proxy_open
- `urllib.request.Request(url, data=None, headers={}, origin_req_host=None, unverifiable=False, method=None)` — add_header, add_unredirected_header, data, full_url, get_full_url, get_header, get_method, has_header, has_proxy, header_items, remove_header, set_proxy
- `urllib.request.URLopener(proxies=None, **x509)` — addheader, cleanup, close, http_error, http_error_default, open, open_data, open_file, open_ftp, open_http, open_https, open_local_file, open_unknown, open_unknown_proxy, retrieve, version
- `urllib.request.UnknownHandler()` — add_parent, close, handler_order, unknown_open

### urllib.response

_подмодуль · импорт 103 мс_

> Response classes used by urllib.

Классы (4):

- `urllib.response.addbase(fp)` — close
- `urllib.response.addclosehook(fp, closehook, *hookargs)` — close
- `urllib.response.addinfo(fp, headers)` — close, info
- `urllib.response.addinfourl(fp, headers, url, code=None)` — close, getcode, geturl, info, status

### urllib.robotparser

_подмодуль · импорт 541 мс_

> robotparser.py

Классы (1):

- `urllib.robotparser.RobotFileParser(url='')` — can_fetch, crawl_delay, modified, mtime, parse, read, request_rate, set_url, site_maps

### uuid

_Python · импорт 13 мс_

> UUID objects (universally unique identifiers) according to RFC 4122.

Функции (6):

- `uuid.getnode()`
- `uuid.main()`
- `uuid.uuid1(node=None, clock_seq=None)`
- `uuid.uuid3(namespace, name)`
- `uuid.uuid4()`
- `uuid.uuid5(namespace, name)`

Классы (4):

- `uuid.SafeUUID(*values)` — safe, unknown, unsafe
- `uuid.UUID(hex=None, bytes=None, bytes_le=None, fields=None, int=None, version=None, *, is_safe=<SafeUUID.unknown: None>)` — bytes, bytes_le, clock_seq, clock_seq_hi_variant, clock_seq_low, fields, hex, int, is_safe, node, time, time_hi_version, time_low, time_mid, urn, variant, version
- `uuid.bytes_(iterable_of_ints) -> bytes` — capitalize, center, count, decode, endswith, expandtabs, find, fromhex, hex, index, isalnum, isalpha, isascii, isdigit, islower, isspace, istitle, isupper, join, ljust, lower, lstrip, maketrans, partition, removeprefix, removesuffix, replace, rfind, rindex, rjust, rpartition, rsplit, rstrip, split, splitlines, startswith, strip, swapcase, title, translate, upper, zfill
- `uuid.int_([x]) -> integer` — as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes

Константы и данные: `uuid.NAMESPACE_DNS` (UUID), `uuid.NAMESPACE_OID` (UUID), `uuid.NAMESPACE_URL` (UUID), `uuid.NAMESPACE_X500` (UUID), `uuid.RESERVED_FUTURE` = `'reserved for future definition'`, `uuid.RESERVED_MICROSOFT` = `'reserved for Microsoft compatibility'`, `uuid.RESERVED_NCS` = `'reserved for NCS compatibility'`, `uuid.RFC_4122` = `'specified in RFC 4122'`.

### warnings

_Python · импорт 0.03 мс_

> Python part of the warnings subsystem.

Функции (7):

- `warnings.filterwarnings(action, message='', category=<class 'Warning'>, module='', lineno=0, append=False)`
- `warnings.formatwarning(message, category, filename, lineno, line=None)`
- `warnings.resetwarnings()`
- `warnings.showwarning(message, category, filename, lineno, file=None, line=None)`
- `warnings.simplefilter(action, category=<class 'Warning'>, lineno=0, append=False)`
- `warnings.warn(message, category=None, stacklevel=1, source=None, *, skip_file_prefixes=())`
- `warnings.warn_explicit(…)`

Классы (2):

- `warnings.catch_warnings(*, record=False, module=None, action=None, category=<class 'Warning'>, lineno=0, append=False)`
- `warnings.deprecated(message: str, /, *, category: type[Warning] | None = <class 'DeprecationWarning'>, stacklevel: int = 1) -> None`

### weakref

_Python · импорт 15 мс_

> Weak reference support for Python.

Функции (3):

- `weakref.getweakrefcount(object, /)`
- `weakref.getweakrefs(object, /)`
- `weakref.proxy(object, callback=None, /)`

Классы (9):

- `weakref.CallableProxyType()`
- `weakref.ProxyType()`
- `weakref.ReferenceType(…)`
- `weakref.WeakKeyDictionary(dict=None)` — clear, copy, get, items, keyrefs, keys, pop, popitem, setdefault, update, values
- `weakref.WeakMethod(meth, callback=None)`
- `weakref.WeakSet(data=None)` — add, clear, copy, difference, difference_update, discard, intersection, intersection_update, isdisjoint, issubset, issuperset, pop, remove, symmetric_difference, symmetric_difference_update, union, update
- `weakref.WeakValueDictionary(other=(), /, **kw)` — clear, copy, get, items, itervaluerefs, keys, pop, popitem, setdefault, update, valuerefs, values
- `weakref.finalize(obj, func, /, *args, **kwargs)` — alive, atexit, detach, peek
- `weakref.ref(…)`

Константы и данные: `weakref.ProxyTypes` (tuple).

### wsgiref

_пакет · импорт 2.7 мс_

> wsgiref -- a WSGI (PEP 3333) Reference Library

Подмодули: `wsgiref.handlers`, `wsgiref.headers`, `wsgiref.simple_server`, `wsgiref.types`, `wsgiref.util`, `wsgiref.validate`.

### wsgiref.handlers

_подмодуль · импорт 23 мс_

> Base classes for server/gateway implementations

Функции (1):

- `wsgiref.handlers.read_environ()`

Классы (5):

- `wsgiref.handlers.BaseCGIHandler(stdin, stdout, stderr, environ, multithread=True, multiprocess=False)` — add_cgi_vars, bytes_sent, cleanup_headers, client_is_modern, close, error_body, error_headers, error_output, error_status, finish_content, finish_response, get_scheme, get_stderr, get_stdin, handle_error, headers, headers_class, headers_sent, http_version, log_exception, origin_server, os_environ, result, result_is_file, run, send_headers, send_preamble, sendfile, server_software, set_content_length, setup_environ, start_response, status, traceback_limit, write, wsgi_file_wrapper, wsgi_multiprocess, wsgi_multithread, wsgi_run_once, wsgi_version
- `wsgiref.handlers.BaseHandler()` — add_cgi_vars, bytes_sent, cleanup_headers, client_is_modern, close, error_body, error_headers, error_output, error_status, finish_content, finish_response, get_scheme, get_stderr, get_stdin, handle_error, headers, headers_class, headers_sent, http_version, log_exception, origin_server, os_environ, result, result_is_file, run, send_headers, send_preamble, sendfile, server_software, set_content_length, setup_environ, start_response, status, traceback_limit, write, wsgi_file_wrapper, wsgi_multiprocess, wsgi_multithread, wsgi_run_once, wsgi_version
- `wsgiref.handlers.CGIHandler()` — add_cgi_vars, bytes_sent, cleanup_headers, client_is_modern, close, error_body, error_headers, error_output, error_status, finish_content, finish_response, get_scheme, get_stderr, get_stdin, handle_error, headers, headers_class, headers_sent, http_version, log_exception, origin_server, os_environ, result, result_is_file, run, send_headers, send_preamble, sendfile, server_software, set_content_length, setup_environ, start_response, status, traceback_limit, write, wsgi_file_wrapper, wsgi_multiprocess, wsgi_multithread, wsgi_run_once, wsgi_version
- `wsgiref.handlers.IISCGIHandler()` — add_cgi_vars, bytes_sent, cleanup_headers, client_is_modern, close, error_body, error_headers, error_output, error_status, finish_content, finish_response, get_scheme, get_stderr, get_stdin, handle_error, headers, headers_class, headers_sent, http_version, log_exception, origin_server, os_environ, result, result_is_file, run, send_headers, send_preamble, sendfile, server_software, set_content_length, setup_environ, start_response, status, traceback_limit, write, wsgi_file_wrapper, wsgi_multiprocess, wsgi_multithread, wsgi_run_once, wsgi_version
- `wsgiref.handlers.SimpleHandler(stdin, stdout, stderr, environ, multithread=True, multiprocess=False)` — add_cgi_vars, bytes_sent, cleanup_headers, client_is_modern, close, error_body, error_headers, error_output, error_status, finish_content, finish_response, get_scheme, get_stderr, get_stdin, handle_error, headers, headers_class, headers_sent, http_version, log_exception, origin_server, os_environ, result, result_is_file, run, send_headers, send_preamble, sendfile, server_software, set_content_length, setup_environ, start_response, status, traceback_limit, write, wsgi_file_wrapper, wsgi_multiprocess, wsgi_multithread, wsgi_run_once, wsgi_version

### wsgiref.headers

_подмодуль · импорт 11 мс_

> Manage HTTP Response Headers

Классы (1):

- `wsgiref.headers.Headers(headers=None)` — add_header, get, get_all, items, keys, setdefault, values

Константы и данные: `wsgiref.headers.tspecials` (Pattern).

### wsgiref.simple_server

_подмодуль · импорт 592 мс_

> BaseHTTPServer that implements the Python WSGI protocol (PEP 3333)

Функции (2):

- `wsgiref.simple_server.demo_app(environ, start_response)`
- `wsgiref.simple_server.make_server(host, port, app, server_class=<class 'wsgiref.simple_server.WSGIServer'>, handler_class=<class 'wsgiref.simple_server.WSGIRequestHandler'>)`

Классы (2):

- `wsgiref.simple_server.WSGIRequestHandler(request, client_address, server)` — MessageClass, address_string, date_time_string, default_request_version, disable_nagle_algorithm, end_headers, error_content_type, error_message_format, finish, flush_headers, get_environ, get_stderr, handle, handle_expect_100, handle_one_request, log_date_time_string, log_error, log_message, log_request, monthname, parse_request, protocol_version, rbufsize, responses, send_error, send_header, send_response, send_response_only, server_version, setup, sys_version, timeout, version_string, wbufsize, weekdayname
- `wsgiref.simple_server.WSGIServer(server_address, RequestHandlerClass, bind_and_activate=True)` — address_family, allow_reuse_address, allow_reuse_port, application, close_request, fileno, finish_request, get_app, get_request, handle_error, handle_request, handle_timeout, process_request, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, set_app, setup_environ, shutdown, shutdown_request, socket_type, timeout, verify_request

### wsgiref.types

_подмодуль · импорт 73 мс_

> WSGI-related types for static type checking

Функции (2):

- `wsgiref.types.WSGIApplication(*args, **kwargs)`
- `wsgiref.types.WSGIEnvironment(*args, **kwargs)`

Классы (4):

- `wsgiref.types.ErrorStream(*args, **kwargs)` — flush, write, writelines
- `wsgiref.types.FileWrapper(*args, **kwargs)`
- `wsgiref.types.InputStream(*args, **kwargs)` — read, readline, readlines
- `wsgiref.types.StartResponse(*args, **kwargs)`

### wsgiref.util

_подмодуль · импорт 7.0 мс_

> Miscellaneous WSGI-related Utilities

Функции (6):

- `wsgiref.util.application_uri(environ)`
- `wsgiref.util.guess_scheme(environ)`
- `wsgiref.util.is_hop_by_hop(header_name)`
- `wsgiref.util.request_uri(environ, include_query=True)`
- `wsgiref.util.setup_testing_defaults(environ)`
- `wsgiref.util.shift_path_info(environ)`

Классы (1):

- `wsgiref.util.FileWrapper(filelike, blksize=8192)`

### wsgiref.validate

_подмодуль · импорт 11 мс_

> Middleware to check for obedience to the WSGI specification.

Функции (1):

- `wsgiref.validate.validator(application)`

### xml

_пакет · импорт 2.6 мс_

> Core XML support for Python.

Подмодули: `xml.dom`, `xml.etree`, `xml.parsers`, `xml.sax`.

Константы и данные: `xml.dom` (нет: AttributeError), `xml.sax` (нет: AttributeError).

### xml.dom

_пакет · импорт 14 мс_

> W3C Document Object Model implementation for Python.

Подмодули: `xml.dom.NodeFilter`, `xml.dom.domreg`, `xml.dom.expatbuilder`, `xml.dom.minicompat`, `xml.dom.minidom`, `xml.dom.pulldom`, `xml.dom.xmlbuilder`.

Функции (2):

- `xml.dom.getDOMImplementation(name=None, features=())`
- `xml.dom.registerDOMImplementation(name, factory)`

Классы (2):

- `xml.dom.Node()` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE
- `xml.dom.UserDataHandler()` — NODE_CLONED, NODE_DELETED, NODE_IMPORTED, NODE_RENAMED

Исключения: `xml.dom.DOMException`, `xml.dom.DomstringSizeErr`, `xml.dom.HierarchyRequestErr`, `xml.dom.IndexSizeErr`, `xml.dom.InuseAttributeErr`, `xml.dom.InvalidAccessErr`, `xml.dom.InvalidCharacterErr`, `xml.dom.InvalidModificationErr`, `xml.dom.InvalidStateErr`, `xml.dom.NamespaceErr`, `xml.dom.NoDataAllowedErr`, `xml.dom.NoModificationAllowedErr`, `xml.dom.NotFoundErr`, `xml.dom.NotSupportedErr`, `xml.dom.SyntaxErr`, `xml.dom.ValidationErr`, `xml.dom.WrongDocumentErr`.

Константы и данные: `xml.dom.DOMSTRING_SIZE_ERR` = `2`, `xml.dom.EMPTY_NAMESPACE` (NoneType), `xml.dom.EMPTY_PREFIX` (NoneType), `xml.dom.HIERARCHY_REQUEST_ERR` = `3`, `xml.dom.INDEX_SIZE_ERR` = `1`, `xml.dom.INUSE_ATTRIBUTE_ERR` = `10`, `xml.dom.INVALID_ACCESS_ERR` = `15`, `xml.dom.INVALID_CHARACTER_ERR` = `5`, `xml.dom.INVALID_MODIFICATION_ERR` = `13`, `xml.dom.INVALID_STATE_ERR` = `11`, `xml.dom.NAMESPACE_ERR` = `14`, `xml.dom.NOT_FOUND_ERR` = `8`, `xml.dom.NOT_SUPPORTED_ERR` = `9`, `xml.dom.NO_DATA_ALLOWED_ERR` = `6`, `xml.dom.NO_MODIFICATION_ALLOWED_ERR` = `7`, `xml.dom.SYNTAX_ERR` = `12`, `xml.dom.VALIDATION_ERR` = `16`, `xml.dom.WRONG_DOCUMENT_ERR` = `4`, `xml.dom.XHTML_NAMESPACE` = `'http://www.w3.org/1999/xhtml'`, `xml.dom.XMLNS_NAMESPACE` = `'http://www.w3.org/2000/xmlns/'`, `xml.dom.XML_NAMESPACE` = `'http://www.w3.org/XML/1998/namespace'`.

### xml.dom.NodeFilter

_подмодуль · импорт 20 мс_

Классы (1):

- `xml.dom.NodeFilter.NodeFilter()` — FILTER_ACCEPT, FILTER_REJECT, FILTER_SKIP, SHOW_ALL, SHOW_ATTRIBUTE, SHOW_CDATA_SECTION, SHOW_COMMENT, SHOW_DOCUMENT, SHOW_DOCUMENT_FRAGMENT, SHOW_DOCUMENT_TYPE, SHOW_ELEMENT, SHOW_ENTITY, SHOW_ENTITY_REFERENCE, SHOW_NOTATION, SHOW_PROCESSING_INSTRUCTION, SHOW_TEXT, acceptNode

### xml.dom.domreg

_подмодуль · импорт 15 мс_

> Registration facilities for DOM. This module should not be used

Функции (2):

- `xml.dom.domreg.getDOMImplementation(name=None, features=())`
- `xml.dom.domreg.registerDOMImplementation(name, factory)`

Константы и данные: `xml.dom.domreg.registered` (dict), `xml.dom.domreg.well_known_implementations` (dict).

### xml.dom.expatbuilder

_подмодуль · импорт 98 мс_

> Facility to use the Expat parser to load a minidom instance

Функции (5):

- `xml.dom.expatbuilder.makeBuilder(options)`
- `xml.dom.expatbuilder.parse(file, namespaces=True)`
- `xml.dom.expatbuilder.parseFragment(file, context, namespaces=True)`
- `xml.dom.expatbuilder.parseFragmentString(string, context, namespaces=True)`
- `xml.dom.expatbuilder.parseString(string, namespaces=True)`

Классы (13):

- `xml.dom.expatbuilder.ElementInfo(tagName, model=None)` — getAttributeType, getAttributeTypeNS, isElementContent, isEmpty, isId, isIdNS, tagName
- `xml.dom.expatbuilder.ExpatBuilder(options=None)` — attlist_decl_handler, character_data_handler, character_data_handler_cdata, comment_handler, createParser, element_decl_handler, end_cdata_section_handler, end_doctype_decl_handler, end_element_handler, entity_decl_handler, external_entity_ref_handler, first_element_handler, getParser, install, notation_decl_handler, parseFile, parseString, pi_handler, reset, start_cdata_section_handler, start_doctype_decl_handler, start_element_handler, xml_decl_handler
- `xml.dom.expatbuilder.ExpatBuilderNS(options=None)` — attlist_decl_handler, character_data_handler, character_data_handler_cdata, comment_handler, createParser, element_decl_handler, end_cdata_section_handler, end_doctype_decl_handler, end_element_handler, entity_decl_handler, external_entity_ref_handler, first_element_handler, getParser, install, notation_decl_handler, parseFile, parseString, pi_handler, reset, start_cdata_section_handler, start_doctype_decl_handler, start_element_handler, start_namespace_decl_handler, xml_decl_handler
- `xml.dom.expatbuilder.FilterCrutch(builder)`
- `xml.dom.expatbuilder.FilterVisibilityController(filter)` — acceptNode, filter, startContainer
- `xml.dom.expatbuilder.FragmentBuilder(context, options=None)` — attlist_decl_handler, character_data_handler, character_data_handler_cdata, comment_handler, createParser, element_decl_handler, end_cdata_section_handler, end_doctype_decl_handler, end_element_handler, entity_decl_handler, external_entity_ref_handler, first_element_handler, getParser, install, notation_decl_handler, parseFile, parseString, pi_handler, reset, start_cdata_section_handler, start_doctype_decl_handler, start_element_handler, xml_decl_handler
- `xml.dom.expatbuilder.FragmentBuilderNS(context, options=None)` — attlist_decl_handler, character_data_handler, character_data_handler_cdata, comment_handler, createParser, element_decl_handler, end_cdata_section_handler, end_doctype_decl_handler, end_element_handler, entity_decl_handler, external_entity_ref_handler, first_element_handler, getParser, install, notation_decl_handler, parseFile, parseString, pi_handler, reset, start_cdata_section_handler, start_doctype_decl_handler, start_element_handler, start_namespace_decl_handler, xml_decl_handler
- `xml.dom.expatbuilder.InternalSubsetExtractor(options=None)` — attlist_decl_handler, character_data_handler, character_data_handler_cdata, comment_handler, createParser, element_decl_handler, end_cdata_section_handler, end_doctype_decl_handler, end_element_handler, entity_decl_handler, external_entity_ref_handler, first_element_handler, getParser, getSubset, install, notation_decl_handler, parseFile, parseString, pi_handler, reset, start_cdata_section_handler, start_doctype_decl_handler, start_element_handler, subset, xml_decl_handler
- `xml.dom.expatbuilder.Namespaces()` — createParser, end_element_handler, install, start_element_handler, start_namespace_decl_handler
- `xml.dom.expatbuilder.Node()` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE
- `xml.dom.expatbuilder.NodeFilter()` — FILTER_ACCEPT, FILTER_REJECT, FILTER_SKIP, SHOW_ALL, SHOW_ATTRIBUTE, SHOW_CDATA_SECTION, SHOW_COMMENT, SHOW_DOCUMENT, SHOW_DOCUMENT_FRAGMENT, SHOW_DOCUMENT_TYPE, SHOW_ELEMENT, SHOW_ENTITY, SHOW_ENTITY_REFERENCE, SHOW_NOTATION, SHOW_PROCESSING_INSTRUCTION, SHOW_TEXT, acceptNode
- `xml.dom.expatbuilder.Rejecter(builder)` — end_element_handler, start_element_handler
- `xml.dom.expatbuilder.Skipper(builder)` — end_element_handler, start_element_handler

Исключения: `xml.dom.expatbuilder.ParseEscape`.

Константы и данные: `xml.dom.expatbuilder.CDATA_SECTION_NODE` = `4`, `xml.dom.expatbuilder.DOCUMENT_NODE` = `9`, `xml.dom.expatbuilder.EMPTY_NAMESPACE` (NoneType), `xml.dom.expatbuilder.EMPTY_PREFIX` (NoneType), `xml.dom.expatbuilder.FILTER_ACCEPT` = `1`, `xml.dom.expatbuilder.FILTER_INTERRUPT` = `4`, `xml.dom.expatbuilder.FILTER_REJECT` = `2`, `xml.dom.expatbuilder.FILTER_SKIP` = `3`, `xml.dom.expatbuilder.TEXT_NODE` = `3`, `xml.dom.expatbuilder.XMLNS_NAMESPACE` = `'http://www.w3.org/2000/xmlns/'`, `xml.dom.expatbuilder.theDOMImplementation` (DOMImplementation).

### xml.dom.minicompat

_подмодуль · импорт 21 мс_

> Python version compatibility support for minidom.

Функции (1):

- `xml.dom.minicompat.defproperty(klass, name, doc)`

Классы (2):

- `xml.dom.minicompat.EmptyNodeList(iterable=(), /)` — count, index, item, length
- `xml.dom.minicompat.NodeList(iterable=(), /)` — append, clear, copy, count, extend, index, insert, item, length, pop, remove, reverse, sort

Константы и данные: `xml.dom.minicompat.StringTypes` (tuple).

### xml.dom.minidom

_подмодуль · импорт 73 мс_

> Simple implementation of the Level 1 DOM.

Функции (4):

- `xml.dom.minidom.defproperty(klass, name, doc)`
- `xml.dom.minidom.getDOMImplementation(features=None)`
- `xml.dom.minidom.parse(file, parser=None, bufsize=None)`
- `xml.dom.minidom.parseString(string, parser=None)`

Классы (25):

- `xml.dom.minidom.Attr(qName, namespaceURI=None, localName=None, prefix=None)` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, attributes, childNodes, cloneNode, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, isId, isSameNode, isSupported, lastChild, localName, name, namespaceURI, nextSibling, nodeName, nodeType, nodeValue, normalize, ownerDocument, ownerElement, parentNode, prefix, previousSibling, removeChild, replaceChild, schemaType, setUserData, specified, toprettyxml, toxml, unlink, value
- `xml.dom.minidom.AttributeList(attrs, attrsNS, ownerElement)` — get, getNamedItem, getNamedItemNS, item, items, itemsNS, keys, keysNS, length, removeNamedItem, removeNamedItemNS, setNamedItem, setNamedItemNS, values
- `xml.dom.minidom.CDATASection()` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, appendData, attributes, childNodes, cloneNode, data, deleteData, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, insertData, isSameNode, isSupported, isWhitespaceInElementContent, lastChild, length, localName, namespaceURI, nextSibling, nodeName, nodeType, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, removeChild, replaceChild, replaceData, replaceWholeText, setUserData, splitText, substringData, toprettyxml, toxml, unlink, wholeText, writexml
- `xml.dom.minidom.CharacterData()` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, appendData, attributes, childNodes, cloneNode, data, deleteData, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, insertData, isSameNode, isSupported, lastChild, length, localName, namespaceURI, nextSibling, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, removeChild, replaceChild, replaceData, setUserData, substringData, toprettyxml, toxml, unlink
- `xml.dom.minidom.Childless()` — appendChild, attributes, childNodes, firstChild, hasChildNodes, insertBefore, lastChild, normalize, removeChild, replaceChild
- `xml.dom.minidom.Comment(data)` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, appendData, attributes, childNodes, cloneNode, data, deleteData, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, insertData, isSameNode, isSupported, lastChild, length, localName, namespaceURI, nextSibling, nodeName, nodeType, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, removeChild, replaceChild, replaceData, setUserData, substringData, toprettyxml, toxml, unlink, writexml
- `xml.dom.minidom.DOMImplementation()` — MODE_ASYNCHRONOUS, MODE_SYNCHRONOUS, createDOMBuilder, createDOMInputSource, createDOMWriter, createDocument, createDocumentType, getInterface, hasFeature
- `xml.dom.minidom.DOMImplementationLS()` — MODE_ASYNCHRONOUS, MODE_SYNCHRONOUS, createDOMBuilder, createDOMInputSource, createDOMWriter
- `xml.dom.minidom.Document()` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, abort, actualEncoding, appendChild, async_, attributes, childNodes, cloneNode, createAttribute, createAttributeNS, createCDATASection, createComment, createDocumentFragment, createElement, createElementNS, createProcessingInstruction, createTextNode, doctype, documentElement, documentURI, encoding, errorHandler, firstChild, getElementById, getElementsByTagName, getElementsByTagNameNS, getInterface, getUserData, hasChildNodes, implementation, importNode, insertBefore, isSameNode, isSupported, lastChild, load, loadXML, localName, namespaceURI, nextSibling, nodeName, nodeType, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, removeChild, renameNode, replaceChild, saveXML, setUserData, standalone, strictErrorChecking, toprettyxml, toxml, unlink, version, writexml
- `xml.dom.minidom.DocumentFragment()` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, attributes, cloneNode, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, isSameNode, isSupported, lastChild, localName, namespaceURI, nextSibling, nodeName, nodeType, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, removeChild, replaceChild, setUserData, toprettyxml, toxml, unlink
- `xml.dom.minidom.DocumentLS()` — abort, async_, load, loadXML, saveXML
- `xml.dom.minidom.DocumentType(qualifiedName)` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, attributes, childNodes, cloneNode, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, internalSubset, isSameNode, isSupported, lastChild, localName, name, namespaceURI, nextSibling, nodeType, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, publicId, removeChild, replaceChild, setUserData, systemId, toprettyxml, toxml, unlink, writexml
- `xml.dom.minidom.Element(tagName, namespaceURI=None, prefix=None, localName=None)` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, attributes, childNodes, cloneNode, firstChild, getAttribute, getAttributeNS, getAttributeNode, getAttributeNodeNS, getElementsByTagName, getElementsByTagNameNS, getInterface, getUserData, hasAttribute, hasAttributeNS, hasAttributes, hasChildNodes, insertBefore, isSameNode, isSupported, lastChild, localName, namespaceURI, nextSibling, nodeName, nodeType, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, removeAttribute, removeAttributeNS, removeAttributeNode, removeAttributeNodeNS, removeChild, replaceChild, schemaType, setAttribute, setAttributeNS, setAttributeNode, setAttributeNodeNS, setIdAttribute, setIdAttributeNS, setIdAttributeNode, setUserData, tagName, toprettyxml, toxml, unlink, writexml
- `xml.dom.minidom.ElementInfo(name)` — getAttributeType, getAttributeTypeNS, isElementContent, isEmpty, isId, isIdNS, tagName
- `xml.dom.minidom.EmptyNodeList(iterable=(), /)` — count, index, item, length
- `xml.dom.minidom.Entity(name, publicId, systemId, notation)` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, actualEncoding, appendChild, attributes, cloneNode, encoding, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, isSameNode, isSupported, lastChild, localName, namespaceURI, nextSibling, nodeType, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, publicId, removeChild, replaceChild, setUserData, systemId, toprettyxml, toxml, unlink, version
- `xml.dom.minidom.Identified()` — publicId, systemId
- `xml.dom.minidom.NamedNodeMap(attrs, attrsNS, ownerElement)` — get, getNamedItem, getNamedItemNS, item, items, itemsNS, keys, keysNS, length, removeNamedItem, removeNamedItemNS, setNamedItem, setNamedItemNS, values
- `xml.dom.minidom.Node()` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, cloneNode, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, isSameNode, isSupported, lastChild, localName, namespaceURI, nextSibling, normalize, ownerDocument, parentNode, prefix, previousSibling, removeChild, replaceChild, setUserData, toprettyxml, toxml, unlink
- `xml.dom.minidom.NodeList(iterable=(), /)` — append, clear, copy, count, extend, index, insert, item, length, pop, remove, reverse, sort
- `xml.dom.minidom.Notation(name, publicId, systemId)` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, attributes, childNodes, cloneNode, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, isSameNode, isSupported, lastChild, localName, namespaceURI, nextSibling, nodeType, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, publicId, removeChild, replaceChild, setUserData, systemId, toprettyxml, toxml, unlink
- `xml.dom.minidom.ProcessingInstruction(target, data)` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, attributes, childNodes, cloneNode, data, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, isSameNode, isSupported, lastChild, localName, namespaceURI, nextSibling, nodeName, nodeType, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, removeChild, replaceChild, setUserData, target, toprettyxml, toxml, unlink, writexml
- `xml.dom.minidom.ReadOnlySequentialNamedNodeMap(seq=())` — getNamedItem, getNamedItemNS, item, length, removeNamedItem, removeNamedItemNS, setNamedItem, setNamedItemNS
- `xml.dom.minidom.Text()` — ATTRIBUTE_NODE, CDATA_SECTION_NODE, COMMENT_NODE, DOCUMENT_FRAGMENT_NODE, DOCUMENT_NODE, DOCUMENT_TYPE_NODE, ELEMENT_NODE, ENTITY_NODE, ENTITY_REFERENCE_NODE, NOTATION_NODE, PROCESSING_INSTRUCTION_NODE, TEXT_NODE, appendChild, appendData, attributes, childNodes, cloneNode, data, deleteData, firstChild, getInterface, getUserData, hasChildNodes, insertBefore, insertData, isSameNode, isSupported, isWhitespaceInElementContent, lastChild, length, localName, namespaceURI, nextSibling, nodeName, nodeType, nodeValue, normalize, ownerDocument, parentNode, prefix, previousSibling, removeChild, replaceChild, replaceData, replaceWholeText, setUserData, splitText, substringData, toprettyxml, toxml, unlink, wholeText, writexml
- `xml.dom.minidom.TypeInfo(namespace, name)` — name, namespace

Константы и данные: `xml.dom.minidom.EMPTY_NAMESPACE` (NoneType), `xml.dom.minidom.EMPTY_PREFIX` (NoneType), `xml.dom.minidom.StringTypes` (tuple), `xml.dom.minidom.XMLNS_NAMESPACE` = `'http://www.w3.org/2000/xmlns/'`.

### xml.dom.pulldom

_подмодуль · импорт 41 мс_

> Support for building partial DOM trees from SAX events.

Функции (2):

- `xml.dom.pulldom.parse(stream_or_string, parser=None, bufsize=None)`
- `xml.dom.pulldom.parseString(string, parser=None)`

Классы (4):

- `xml.dom.pulldom.DOMEventStream(stream, parser, bufsize)` — clear, expandNode, getEvent, reset
- `xml.dom.pulldom.ErrorHandler()` — error, fatalError, warning
- `xml.dom.pulldom.PullDOM(documentFactory=None)` — buildDocument, characters, clear, comment, document, endDocument, endElement, endElementNS, endPrefixMapping, ignorableWhitespace, pop, processingInstruction, setDocumentLocator, skippedEntity, startDocument, startElement, startElementNS, startPrefixMapping
- `xml.dom.pulldom.SAX2DOM(documentFactory=None)` — buildDocument, characters, clear, comment, document, endDocument, endElement, endElementNS, endPrefixMapping, ignorableWhitespace, pop, processingInstruction, setDocumentLocator, skippedEntity, startDocument, startElement, startElementNS, startPrefixMapping

Константы и данные: `xml.dom.pulldom.CHARACTERS` = `'CHARACTERS'`, `xml.dom.pulldom.COMMENT` = `'COMMENT'`, `xml.dom.pulldom.END_DOCUMENT` = `'END_DOCUMENT'`, `xml.dom.pulldom.END_ELEMENT` = `'END_ELEMENT'`, `xml.dom.pulldom.IGNORABLE_WHITESPACE` = `'IGNORABLE_WHITESPACE'`, `xml.dom.pulldom.PROCESSING_INSTRUCTION` = `'PROCESSING_INSTRUCTION'`, `xml.dom.pulldom.START_DOCUMENT` = `'START_DOCUMENT'`, `xml.dom.pulldom.START_ELEMENT` = `'START_ELEMENT'`, `xml.dom.pulldom.default_bufsize` = `16364`.

### xml.dom.xmlbuilder

_подмодуль · импорт 41 мс_

> Implementation of the DOM Level 3 'LS-Load' feature.

Классы (3):

- `xml.dom.xmlbuilder.DOMBuilder()` — ACTION_APPEND_AS_CHILDREN, ACTION_INSERT_AFTER, ACTION_INSERT_BEFORE, ACTION_REPLACE, canSetFeature, entityResolver, errorHandler, filter, getFeature, parse, parseURI, parseWithContext, setFeature, supportsFeature
- `xml.dom.xmlbuilder.DOMEntityResolver()` — resolveEntity
- `xml.dom.xmlbuilder.DOMInputSource()` — baseURI, byteStream, characterStream, encoding, publicId, stringData, systemId

### xml.etree

_пакет · импорт 6.0 мс_

Подмодули: `xml.etree.ElementInclude`, `xml.etree.ElementPath`, `xml.etree.ElementTree`, `xml.etree.cElementTree`.

### xml.etree.ElementInclude

_подмодуль · импорт 120 мс_

Функции (2):

- `xml.etree.ElementInclude.default_loader(href, parse, encoding=None)`
- `xml.etree.ElementInclude.include(elem, loader=None, base_url=None, max_depth=6)`

Исключения: `xml.etree.ElementInclude.FatalIncludeError`, `xml.etree.ElementInclude.LimitedRecursiveIncludeError`.

Константы и данные: `xml.etree.ElementInclude.DEFAULT_MAX_INCLUSION_DEPTH` = `6`, `xml.etree.ElementInclude.XINCLUDE` = `'{http://www.w3.org/2001/XInclude}'`, `xml.etree.ElementInclude.XINCLUDE_FALLBACK` = `'{http://www.w3.org/2001/XInclude}fallback'`, `xml.etree.ElementInclude.XINCLUDE_INCLUDE` = `'{http://www.w3.org/2001/XInclude}include'`.

### xml.etree.ElementPath

_подмодуль · импорт 26 мс_

Функции (12):

- `xml.etree.ElementPath.find(elem, path, namespaces=None)`
- `xml.etree.ElementPath.findall(elem, path, namespaces=None)`
- `xml.etree.ElementPath.findtext(elem, path, default=None, namespaces=None)`
- `xml.etree.ElementPath.get_parent_map(context)`
- `xml.etree.ElementPath.iterfind(elem, path, namespaces=None)`
- `xml.etree.ElementPath.prepare_child(next, token)`
- `xml.etree.ElementPath.prepare_descendant(next, token)`
- `xml.etree.ElementPath.prepare_parent(next, token)`
- `xml.etree.ElementPath.prepare_predicate(next, token)`
- `xml.etree.ElementPath.prepare_self(next, token)`
- `xml.etree.ElementPath.prepare_star(next, token)`
- `xml.etree.ElementPath.xpath_tokenizer(pattern, namespaces=None)`

Константы и данные: `xml.etree.ElementPath.ops` (dict), `xml.etree.ElementPath.xpath_tokenizer_re` (Pattern).

### xml.etree.ElementTree

_подмодуль · импорт 53 мс_

> Lightweight XML support for Python.

Функции (17):

- `xml.etree.ElementTree.Comment(text=None)`
- `xml.etree.ElementTree.PI(target, text=None)`
- `xml.etree.ElementTree.ProcessingInstruction(target, text=None)`
- `xml.etree.ElementTree.SubElement(…)`
- `xml.etree.ElementTree.XML(text, parser=None)`
- `xml.etree.ElementTree.XMLID(text, parser=None)`
- `xml.etree.ElementTree.canonicalize(xml_data=None, *, out=None, from_file=None, **options)`
- `xml.etree.ElementTree.dump(elem)`
- `xml.etree.ElementTree.fromstring(text, parser=None)`
- `xml.etree.ElementTree.fromstringlist(sequence, parser=None)`
- `xml.etree.ElementTree.indent(tree, space='  ', level=0)`
- `xml.etree.ElementTree.iselement(element)`
- `xml.etree.ElementTree.iterparse(source, events=None, parser=None)`
- `xml.etree.ElementTree.parse(source, parser=None)`
- `xml.etree.ElementTree.register_namespace(prefix, uri)`
- `xml.etree.ElementTree.tostring(element, encoding=None, method=None, *, xml_declaration=None, default_namespace=None, short_empty_elements=True)`
- `xml.etree.ElementTree.tostringlist(element, encoding=None, method=None, *, xml_declaration=None, default_namespace=None, short_empty_elements=True)`

Классы (7):

- `xml.etree.ElementTree.C14NWriterTarget(write, *, with_comments=False, strip_text=False, rewrite_prefixes=False, qname_aware_tags=None, qname_aware_attrs=None, exclude_attrs=None, exclude_tags=None)` — comment, data, end, pi, start, start_ns
- `xml.etree.ElementTree.Element(…)` — append, attrib, clear, extend, find, findall, findtext, get, insert, items, iter, iterfind, itertext, keys, makeelement, remove, set, tag, tail, text
- `xml.etree.ElementTree.ElementTree(element=None, file=None)` — find, findall, findtext, getroot, iter, iterfind, parse, write, write_c14n
- `xml.etree.ElementTree.QName(text_or_uri, tag=None)`
- `xml.etree.ElementTree.TreeBuilder(…)` — close, comment, data, end, pi, start
- `xml.etree.ElementTree.XMLParser(…)` — close, entity, feed, flush, target, version
- `xml.etree.ElementTree.XMLPullParser(events=None, *, _parser=None)` — close, feed, flush, read_events

Исключения: `xml.etree.ElementTree.ParseError`.

Константы и данные: `xml.etree.ElementTree.VERSION` = `'1.3.0'`.

### xml.etree.cElementTree

_подмодуль · импорт 56 мс_

Функции (16):

- `xml.etree.cElementTree.Comment(text=None)`
- `xml.etree.cElementTree.PI(target, text=None)`
- `xml.etree.cElementTree.ProcessingInstruction(target, text=None)`
- `xml.etree.cElementTree.XML(text, parser=None)`
- `xml.etree.cElementTree.XMLID(text, parser=None)`
- `xml.etree.cElementTree.canonicalize(xml_data=None, *, out=None, from_file=None, **options)`
- `xml.etree.cElementTree.dump(elem)`
- `xml.etree.cElementTree.fromstring(text, parser=None)`
- `xml.etree.cElementTree.fromstringlist(sequence, parser=None)`
- `xml.etree.cElementTree.indent(tree, space='  ', level=0)`
- `xml.etree.cElementTree.iselement(element)`
- `xml.etree.cElementTree.iterparse(source, events=None, parser=None)`
- `xml.etree.cElementTree.parse(source, parser=None)`
- `xml.etree.cElementTree.register_namespace(prefix, uri)`
- `xml.etree.cElementTree.tostring(element, encoding=None, method=None, *, xml_declaration=None, default_namespace=None, short_empty_elements=True)`
- `xml.etree.cElementTree.tostringlist(element, encoding=None, method=None, *, xml_declaration=None, default_namespace=None, short_empty_elements=True)`

Классы (7):

- `xml.etree.cElementTree.C14NWriterTarget(write, *, with_comments=False, strip_text=False, rewrite_prefixes=False, qname_aware_tags=None, qname_aware_attrs=None, exclude_attrs=None, exclude_tags=None)` — comment, data, end, pi, start, start_ns
- `xml.etree.cElementTree.Element(…)` — append, attrib, clear, extend, find, findall, findtext, get, insert, items, iter, iterfind, itertext, keys, makeelement, remove, set, tag, tail, text
- `xml.etree.cElementTree.ElementTree(element=None, file=None)` — find, findall, findtext, getroot, iter, iterfind, parse, write, write_c14n
- `xml.etree.cElementTree.QName(text_or_uri, tag=None)`
- `xml.etree.cElementTree.TreeBuilder(…)` — close, comment, data, end, pi, start
- `xml.etree.cElementTree.XMLParser(…)` — close, entity, feed, flush, target, version
- `xml.etree.cElementTree.XMLPullParser(events=None, *, _parser=None)` — close, feed, flush, read_events

Исключения: `xml.etree.cElementTree.ParseError`.

Константы и данные: `xml.etree.cElementTree.VERSION` = `'1.3.0'`.

### xml.parsers

_пакет · импорт 6.0 мс_

> Python interfaces to XML parsers.

Подмодули: `xml.parsers.expat`.

### xml.parsers.expat

_подмодуль · импорт 16 мс_

> Interface to the Expat non-validating XML parser.

Исключения: `xml.parsers.expat.ExpatError`, `xml.parsers.expat.error`.

Константы и данные: `xml.parsers.expat.EXPAT_VERSION` = `'expat_2.8.5'`, `xml.parsers.expat.XML_PARAM_ENTITY_PARSING_ALWAYS` = `2`, `xml.parsers.expat.XML_PARAM_ENTITY_PARSING_NEVER` = `0`, `xml.parsers.expat.XML_PARAM_ENTITY_PARSING_UNLESS_STANDALONE` = `1`, `xml.parsers.expat.expat_CAPI` (PyCapsule), `xml.parsers.expat.features` (list), `xml.parsers.expat.native_encoding` = `'UTF-8'`, `xml.parsers.expat.version_info` (tuple).

### xml.sax

_пакет · импорт 36 мс_

> Simple API for XML (SAX) implementation for Python.

Подмодули: `xml.sax.expatreader`, `xml.sax.handler`, `xml.sax.saxutils`, `xml.sax.xmlreader`.

Функции (3):

- `xml.sax.make_parser(parser_list=())`
- `xml.sax.parse(source, handler, errorHandler=<xml.sax.handler.ErrorHandler object>)`
- `xml.sax.parseString(string, handler, errorHandler=<xml.sax.handler.ErrorHandler object>)`

Классы (3):

- `xml.sax.ContentHandler()` — characters, endDocument, endElement, endElementNS, endPrefixMapping, ignorableWhitespace, processingInstruction, setDocumentLocator, skippedEntity, startDocument, startElement, startElementNS, startPrefixMapping
- `xml.sax.ErrorHandler()` — error, fatalError, warning
- `xml.sax.InputSource(system_id=None)` — getByteStream, getCharacterStream, getEncoding, getPublicId, getSystemId, setByteStream, setCharacterStream, setEncoding, setPublicId, setSystemId

Исключения: `xml.sax.SAXException`, `xml.sax.SAXNotRecognizedException`, `xml.sax.SAXNotSupportedException`, `xml.sax.SAXParseException`, `xml.sax.SAXReaderNotAvailable`.

Константы и данные: `xml.sax.default_parser_list` (list).

### xml.sax.expatreader

_подмодуль · импорт 589 мс_

> SAX driver for the pyexpat C module.  This driver works with

Функции (1):

- `xml.sax.expatreader.create_parser(*args, **kwargs)`

Классы (4):

- `xml.sax.expatreader.AttributesImpl(attrs)` — copy, get, getLength, getNameByQName, getNames, getQNameByName, getQNames, getType, getValue, getValueByQName, items, keys, values
- `xml.sax.expatreader.AttributesNSImpl(attrs, qnames)` — copy, get, getLength, getNameByQName, getNames, getQNameByName, getQNames, getType, getValue, getValueByQName, items, keys, values
- `xml.sax.expatreader.ExpatLocator(parser)` — getColumnNumber, getLineNumber, getPublicId, getSystemId
- `xml.sax.expatreader.ExpatParser(namespaceHandling=0, bufsize=65516)` — character_data, close, end_element, end_element_ns, end_namespace_decl, external_entity_ref, feed, flush, getColumnNumber, getContentHandler, getDTDHandler, getEntityResolver, getErrorHandler, getFeature, getLineNumber, getProperty, getPublicId, getSystemId, notation_decl, parse, prepareParser, processing_instruction, reset, setContentHandler, setDTDHandler, setEntityResolver, setErrorHandler, setFeature, setLocale, setProperty, skipped_entity_handler, start_doctype_decl, start_element, start_element_ns, start_namespace_decl, unparsed_entity_decl

Исключения: `xml.sax.expatreader.SAXException`, `xml.sax.expatreader.SAXNotRecognizedException`, `xml.sax.expatreader.SAXNotSupportedException`, `xml.sax.expatreader.SAXParseException`, `xml.sax.expatreader.SAXReaderNotAvailable`.

Константы и данные: `xml.sax.expatreader.feature_external_ges` = `'http://xml.org/sax/features/external-general-entities'`, `xml.sax.expatreader.feature_external_pes` = `'http://xml.org/sax/features/external-parameter-entities'`, `xml.sax.expatreader.feature_namespace_prefixes` = `'http://xml.org/sax/features/namespace-prefixes'`, `xml.sax.expatreader.feature_namespaces` = `'http://xml.org/sax/features/namespaces'`, `xml.sax.expatreader.feature_string_interning` = `'http://xml.org/sax/features/string-interning'`, `xml.sax.expatreader.feature_validation` = `'http://xml.org/sax/features/validation'`, `xml.sax.expatreader.property_interning_dict` = `'http://www.python.org/sax/properties/interning-dict'`, `xml.sax.expatreader.property_xml_string` = `'http://xml.org/sax/properties/xml-string'`, `xml.sax.expatreader.version` = `'0.20'`.

### xml.sax.handler

_подмодуль · импорт 25 мс_

> This module contains the core classes of version 2.0 of SAX for Python.

Классы (5):

- `xml.sax.handler.ContentHandler()` — characters, endDocument, endElement, endElementNS, endPrefixMapping, ignorableWhitespace, processingInstruction, setDocumentLocator, skippedEntity, startDocument, startElement, startElementNS, startPrefixMapping
- `xml.sax.handler.DTDHandler()` — notationDecl, unparsedEntityDecl
- `xml.sax.handler.EntityResolver()` — resolveEntity
- `xml.sax.handler.ErrorHandler()` — error, fatalError, warning
- `xml.sax.handler.LexicalHandler()` — comment, endCDATA, endDTD, startCDATA, startDTD

Константы и данные: `xml.sax.handler.all_features` (list), `xml.sax.handler.all_properties` (list), `xml.sax.handler.feature_external_ges` = `'http://xml.org/sax/features/external-general-entities'`, `xml.sax.handler.feature_external_pes` = `'http://xml.org/sax/features/external-parameter-entities'`, `xml.sax.handler.feature_namespace_prefixes` = `'http://xml.org/sax/features/namespace-prefixes'`, `xml.sax.handler.feature_namespaces` = `'http://xml.org/sax/features/namespaces'`, `xml.sax.handler.feature_string_interning` = `'http://xml.org/sax/features/string-interning'`, `xml.sax.handler.feature_validation` = `'http://xml.org/sax/features/validation'`, `xml.sax.handler.property_declaration_handler` = `'http://xml.org/sax/properties/declaration-handler'`, `xml.sax.handler.property_dom_node` = `'http://xml.org/sax/properties/dom-node'`, `xml.sax.handler.property_encoding` = `'http://www.python.org/sax/properties/encoding'`, `xml.sax.handler.property_interning_dict` = `'http://www.python.org/sax/properties/interning-dict'`, `xml.sax.handler.property_lexical_handler` = `'http://xml.org/sax/properties/lexical-handler'`, `xml.sax.handler.property_xml_string` = `'http://xml.org/sax/properties/xml-string'`, `xml.sax.handler.version` = `'2.0beta'`.

### xml.sax.saxutils

_подмодуль · импорт 568 мс_

> A library of useful helper classes to the SAX classes, for the

Функции (4):

- `xml.sax.saxutils.escape(data, entities={})`
- `xml.sax.saxutils.prepare_input_source(source, base='')`
- `xml.sax.saxutils.quoteattr(data, entities={})`
- `xml.sax.saxutils.unescape(data, entities={})`

Классы (2):

- `xml.sax.saxutils.XMLFilterBase(parent=None)` — characters, endDocument, endElement, endElementNS, endPrefixMapping, error, fatalError, getContentHandler, getDTDHandler, getEntityResolver, getErrorHandler, getFeature, getParent, getProperty, ignorableWhitespace, notationDecl, parse, processingInstruction, resolveEntity, setContentHandler, setDTDHandler, setDocumentLocator, setEntityResolver, setErrorHandler, setFeature, setLocale, setParent, setProperty, skippedEntity, startDocument, startElement, startElementNS, startPrefixMapping, unparsedEntityDecl, warning
- `xml.sax.saxutils.XMLGenerator(out=None, encoding='iso-8859-1', short_empty_elements=False)` — characters, endDocument, endElement, endElementNS, endPrefixMapping, ignorableWhitespace, processingInstruction, setDocumentLocator, skippedEntity, startDocument, startElement, startElementNS, startPrefixMapping

### xml.sax.xmlreader

_подмодуль · импорт 25 мс_

> An XML Reader is the SAX 2 name for an XML parser. XML Parsers

Классы (6):

- `xml.sax.xmlreader.AttributesImpl(attrs)` — copy, get, getLength, getNameByQName, getNames, getQNameByName, getQNames, getType, getValue, getValueByQName, items, keys, values
- `xml.sax.xmlreader.AttributesNSImpl(attrs, qnames)` — copy, get, getLength, getNameByQName, getNames, getQNameByName, getQNames, getType, getValue, getValueByQName, items, keys, values
- `xml.sax.xmlreader.IncrementalParser(bufsize=65536)` — close, feed, getContentHandler, getDTDHandler, getEntityResolver, getErrorHandler, getFeature, getProperty, parse, prepareParser, reset, setContentHandler, setDTDHandler, setEntityResolver, setErrorHandler, setFeature, setLocale, setProperty
- `xml.sax.xmlreader.InputSource(system_id=None)` — getByteStream, getCharacterStream, getEncoding, getPublicId, getSystemId, setByteStream, setCharacterStream, setEncoding, setPublicId, setSystemId
- `xml.sax.xmlreader.Locator()` — getColumnNumber, getLineNumber, getPublicId, getSystemId
- `xml.sax.xmlreader.XMLReader()` — getContentHandler, getDTDHandler, getEntityResolver, getErrorHandler, getFeature, getProperty, parse, setContentHandler, setDTDHandler, setEntityResolver, setErrorHandler, setFeature, setLocale, setProperty

Исключения: `xml.sax.xmlreader.SAXNotRecognizedException`, `xml.sax.xmlreader.SAXNotSupportedException`.

### xmlrpc

_пакет · импорт 13 мс_

Подмодули: `xmlrpc.client`, `xmlrpc.server`.

### xmlrpc.client

_подмодуль · импорт 475 мс_

> An XML-RPC client interface for Python.

Функции (6):

- `xmlrpc.client.dumps(params, methodname=None, methodresponse=None, encoding=None, allow_none=False)`
- `xmlrpc.client.escape(s)`
- `xmlrpc.client.getparser(use_datetime=False, use_builtin_types=False)`
- `xmlrpc.client.gzip_decode(data, max_decode=20971520)`
- `xmlrpc.client.gzip_encode(data)`
- `xmlrpc.client.loads(data, use_datetime=False, use_builtin_types=False)`

Классы (14):

- `xmlrpc.client.Binary(data=None)` — decode, encode
- `xmlrpc.client.Boolean(object=False, /)` — as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes
- `xmlrpc.client.DateTime(value=0)` — decode, encode, make_comparable, timetuple
- `xmlrpc.client.ExpatParser(target)` — close, feed
- `xmlrpc.client.GzipDecodedResponse(response)` — close, closed, detach, fileno, flush, isatty, mtime, myfileobj, peek, read, read1, readable, readinto, readinto1, readline, readlines, rewind, seek, seekable, tell, truncate, writable, write, writelines
- `xmlrpc.client.Marshaller(encoding=None, allow_none=False)` — dispatch, dump_array, dump_bool, dump_bytes, dump_datetime, dump_double, dump_instance, dump_int, dump_long, dump_nil, dump_struct, dump_unicode, dumps
- `xmlrpc.client.MultiCall(server)`
- `xmlrpc.client.MultiCallIterator(results)`
- `xmlrpc.client.SafeTransport(use_datetime=False, use_builtin_types=False, *, headers=(), context=None)` — accept_gzip_encoding, close, encode_threshold, get_host_info, getparser, make_connection, parse_response, request, send_content, send_headers, send_request, single_request, user_agent
- `xmlrpc.client.Server(uri, transport=None, encoding=None, verbose=False, allow_none=False, use_datetime=False, use_builtin_types=False, *, headers=(), context=None)`
- `xmlrpc.client.ServerProxy(uri, transport=None, encoding=None, verbose=False, allow_none=False, use_datetime=False, use_builtin_types=False, *, headers=(), context=None)`
- `xmlrpc.client.Transport(use_datetime=False, use_builtin_types=False, *, headers=())` — accept_gzip_encoding, close, encode_threshold, get_host_info, getparser, make_connection, parse_response, request, send_content, send_headers, send_request, single_request, user_agent
- `xmlrpc.client.Unmarshaller(use_datetime=False, use_builtin_types=False)` — close, data, dispatch, end, end_array, end_base64, end_bigdecimal, end_boolean, end_dateTime, end_dispatch, end_double, end_fault, end_int, end_methodName, end_nil, end_params, end_string, end_struct, end_value, getmethodname, start, xml
- `xmlrpc.client.boolean(object=False, /)` — as_integer_ratio, bit_count, bit_length, conjugate, denominator, from_bytes, imag, is_integer, numerator, real, to_bytes

Исключения: `xmlrpc.client.Error`, `xmlrpc.client.Fault`, `xmlrpc.client.ProtocolError`, `xmlrpc.client.ResponseError`.

Константы и данные: `xmlrpc.client.APPLICATION_ERROR` = `-32500`, `xmlrpc.client.FastMarshaller` (NoneType), `xmlrpc.client.FastParser` (NoneType), `xmlrpc.client.FastUnmarshaller` (NoneType), `xmlrpc.client.INTERNAL_ERROR` = `-32603`, `xmlrpc.client.INVALID_ENCODING_CHAR` = `-32702`, `xmlrpc.client.INVALID_METHOD_PARAMS` = `-32602`, `xmlrpc.client.INVALID_XMLRPC` = `-32600`, `xmlrpc.client.MAXINT` = `2147483647`, `xmlrpc.client.METHOD_NOT_FOUND` = `-32601`, `xmlrpc.client.MININT` = `-2147483648`, `xmlrpc.client.NOT_WELLFORMED_ERROR` = `-32700`, `xmlrpc.client.PARSE_ERROR` = `-32700`, `xmlrpc.client.SERVER_ERROR` = `-32600`, `xmlrpc.client.SYSTEM_ERROR` = `-32400`, `xmlrpc.client.TRANSPORT_ERROR` = `-32300`, `xmlrpc.client.UNSUPPORTED_ENCODING` = `-32701`, `xmlrpc.client.WRAPPERS` (tuple).

### xmlrpc.server

_подмодуль · импорт 985 мс_

> XML-RPC Servers.

Функции (6):

- `xmlrpc.server.dumps(params, methodname=None, methodresponse=None, encoding=None, allow_none=False)`
- `xmlrpc.server.gzip_decode(data, max_decode=20971520)`
- `xmlrpc.server.gzip_encode(data)`
- `xmlrpc.server.list_public_methods(obj)`
- `xmlrpc.server.loads(data, use_datetime=False, use_builtin_types=False)`
- `xmlrpc.server.resolve_dotted_attribute(obj, attr, allow_dotted_names=True)`

Классы (10):

- `xmlrpc.server.CGIXMLRPCRequestHandler(allow_none=False, encoding=None, use_builtin_types=False)` — handle_get, handle_request, handle_xmlrpc, register_function, register_instance, register_introspection_functions, register_multicall_functions, system_listMethods, system_methodHelp, system_methodSignature, system_multicall
- `xmlrpc.server.DocCGIXMLRPCRequestHandler()` — generate_html_documentation, handle_get, handle_request, handle_xmlrpc, register_function, register_instance, register_introspection_functions, register_multicall_functions, set_server_documentation, set_server_name, set_server_title, system_listMethods, system_methodHelp, system_methodSignature, system_multicall
- `xmlrpc.server.DocXMLRPCRequestHandler(request, client_address, server)` — MessageClass, accept_encodings, address_string, aepattern, date_time_string, decode_request_content, default_request_version, disable_nagle_algorithm, do_GET, do_POST, encode_threshold, end_headers, error_content_type, error_message_format, finish, flush_headers, handle, handle_expect_100, handle_one_request, is_rpc_path_valid, log_date_time_string, log_error, log_message, log_request, monthname, parse_request, protocol_version, rbufsize, report_404, responses, rpc_paths, send_error, send_header, send_response, send_response_only, server_version, setup, sys_version, timeout, version_string, wbufsize, weekdayname
- `xmlrpc.server.DocXMLRPCServer(addr, requestHandler=<class 'xmlrpc.server.DocXMLRPCRequestHandler'>, logRequests=True, allow_none=False, encoding=None, bind_and_activate=True, use_builtin_types=False)` — address_family, allow_reuse_address, allow_reuse_port, close_request, fileno, finish_request, generate_html_documentation, get_request, handle_error, handle_request, handle_timeout, process_request, register_function, register_instance, register_introspection_functions, register_multicall_functions, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, set_server_documentation, set_server_name, set_server_title, shutdown, shutdown_request, socket_type, system_listMethods, system_methodHelp, system_methodSignature, system_multicall, timeout, verify_request
- `xmlrpc.server.MultiPathXMLRPCServer(addr, requestHandler=<class 'xmlrpc.server.SimpleXMLRPCRequestHandler'>, logRequests=True, allow_none=False, encoding=None, bind_and_activate=True, use_builtin_types=False)` — add_dispatcher, address_family, allow_reuse_address, allow_reuse_port, close_request, fileno, finish_request, get_dispatcher, get_request, handle_error, handle_request, handle_timeout, process_request, register_function, register_instance, register_introspection_functions, register_multicall_functions, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, system_listMethods, system_methodHelp, system_methodSignature, system_multicall, timeout, verify_request
- `xmlrpc.server.ServerHTMLDoc()` — PYTHONDOCS, bigsection, classlink, docclass, docdata, docmodule, docother, docproperty, docroutine, docserver, document, escape, fail, filelink, formattree, formatvalue, getdocloc, grey, heading, index, markup, modpkglink, modulelink, multicolumn, namelink, page, parentlink, preformat, repr, section
- `xmlrpc.server.SimpleXMLRPCDispatcher(allow_none=False, encoding=None, use_builtin_types=False)` — register_function, register_instance, register_introspection_functions, register_multicall_functions, system_listMethods, system_methodHelp, system_methodSignature, system_multicall
- `xmlrpc.server.SimpleXMLRPCRequestHandler(request, client_address, server)` — MessageClass, accept_encodings, address_string, aepattern, date_time_string, decode_request_content, default_request_version, disable_nagle_algorithm, do_POST, encode_threshold, end_headers, error_content_type, error_message_format, finish, flush_headers, handle, handle_expect_100, handle_one_request, is_rpc_path_valid, log_date_time_string, log_error, log_message, log_request, monthname, parse_request, protocol_version, rbufsize, report_404, responses, rpc_paths, send_error, send_header, send_response, send_response_only, server_version, setup, sys_version, timeout, version_string, wbufsize, weekdayname
- `xmlrpc.server.SimpleXMLRPCServer(addr, requestHandler=<class 'xmlrpc.server.SimpleXMLRPCRequestHandler'>, logRequests=True, allow_none=False, encoding=None, bind_and_activate=True, use_builtin_types=False)` — address_family, allow_reuse_address, allow_reuse_port, close_request, fileno, finish_request, get_request, handle_error, handle_request, handle_timeout, process_request, register_function, register_instance, register_introspection_functions, register_multicall_functions, request_queue_size, serve_forever, server_activate, server_bind, server_close, service_actions, shutdown, shutdown_request, socket_type, system_listMethods, system_methodHelp, system_methodSignature, system_multicall, timeout, verify_request
- `xmlrpc.server.XMLRPCDocGenerator()` — generate_html_documentation, set_server_documentation, set_server_name, set_server_title

Исключения: `xmlrpc.server.Fault`.

### zipfile

_пакет · импорт 171 мс_

> Read and write ZIP files.

Функции (1):

- `zipfile.is_zipfile(filename)`

Классы (4):

- `zipfile.Path(root, at='')` — exists, filename, glob, is_dir, is_file, is_symlink, iterdir, joinpath, match, name, open, parent, read_bytes, read_text, relative_to, rglob, stem, suffix, suffixes
- `zipfile.PyZipFile(file, mode='r', compression=0, allowZip64=True, optimize=-1)` — close, comment, extract, extractall, fp, getinfo, infolist, mkdir, namelist, open, printdir, read, setpassword, testzip, write, writepy, writestr
- `zipfile.ZipFile(file, mode='r', compression=0, allowZip64=True, compresslevel=None, *, strict_timestamps=True, metadata_encoding=None)` — close, comment, extract, extractall, fp, getinfo, infolist, mkdir, namelist, open, printdir, read, setpassword, testzip, write, writestr
- `zipfile.ZipInfo(filename='NoName', date_time=(1980, 1, 1, 0, 0, 0))` — CRC, FileHeader, comment, compress_level, compress_size, compress_type, create_system, create_version, date_time, external_attr, extra, extract_version, file_size, filename, flag_bits, from_file, header_offset, internal_attr, is_dir, orig_filename, reserved, volume

Исключения: `zipfile.BadZipFile`, `zipfile.BadZipfile`, `zipfile.LargeZipFile`, `zipfile.error`.

Константы и данные: `zipfile.ZIP_BZIP2` = `12`, `zipfile.ZIP_DEFLATED` = `8`, `zipfile.ZIP_LZMA` = `14`, `zipfile.ZIP_STORED` = `0`.

### zlib

_расширение (.so) · импорт 3.9 мс_

> The functions in this module allow compression and decompression using the

Функции (6):

- `zlib.adler32(data, value=1, /)`
- `zlib.compress(data, /, level=-1, wbits=15)`
- `zlib.compressobj(level=-1, method=8, wbits=15, memLevel=8, strategy=0, zdict=None)`
- `zlib.crc32(data, value=0, /)`
- `zlib.decompress(data, /, wbits=15, bufsize=16384)`
- `zlib.decompressobj(wbits=15, zdict=b'')`

Исключения: `zlib.error`.

Константы и данные: `zlib.DEFLATED` = `8`, `zlib.DEF_BUF_SIZE` = `16384`, `zlib.DEF_MEM_LEVEL` = `8`, `zlib.MAX_WBITS` = `15`, `zlib.ZLIB_RUNTIME_VERSION` = `'1.2.11'`, `zlib.ZLIB_VERSION` = `'1.2.11'`, `zlib.Z_BEST_COMPRESSION` = `9`, `zlib.Z_BEST_SPEED` = `1`, `zlib.Z_BLOCK` = `5`, `zlib.Z_DEFAULT_COMPRESSION` = `-1`, `zlib.Z_DEFAULT_STRATEGY` = `0`, `zlib.Z_FILTERED` = `1`, `zlib.Z_FINISH` = `4`, `zlib.Z_FIXED` = `4`, `zlib.Z_FULL_FLUSH` = `3`, `zlib.Z_HUFFMAN_ONLY` = `2`, `zlib.Z_NO_COMPRESSION` = `0`, `zlib.Z_NO_FLUSH` = `0`, `zlib.Z_PARTIAL_FLUSH` = `1`, `zlib.Z_RLE` = `3`, `zlib.Z_SYNC_FLUSH` = `2`, `zlib.Z_TREES` = `6`.

### zoneinfo

_пакет · импорт 83 мс_

Функции (2):

- `zoneinfo.available_timezones()`
- `zoneinfo.reset_tzpath(to=None)`

Классы (1):

- `zoneinfo.ZoneInfo(…)` — clear_cache, dst, from_file, fromutc, key, no_cache, tzname, utcoffset

Исключения: `zoneinfo.InvalidTZPathWarning`, `zoneinfo.ZoneInfoNotFoundError`.

Константы и данные: `zoneinfo.TZPATH` (tuple).

## 5. `libtensorflowlite_c.so` — TensorFlow Lite C API

Версия (`TfLiteVersion()` на устройстве): **2.10.0**. Нужна logix (нейроблок вендора). **Для скриптов PyScript привязки нет** — из скрипта библиотеку не вызвать. Перечень — для полноты (экспорт `nm -D`, 75 функций).

| функция | объявление | заголовок |
|---|---|---|
| `TfLiteDelegateCreate` | `TfLiteDelegate TfLiteDelegateCreate(void)` | c/common.h |
| `TfLiteFloatArrayCreate` | `TfLiteFloatArray* TfLiteFloatArrayCreate(int size)` | c/common.h |
| `TfLiteFloatArrayFree` | `void TfLiteFloatArrayFree(TfLiteFloatArray* a)` | c/common.h |
| `TfLiteFloatArrayGetSizeInBytes` | `int TfLiteFloatArrayGetSizeInBytes(int size)` | c/common.h |
| `TfLiteIntArrayCopy` | `TfLiteIntArray* TfLiteIntArrayCopy(const TfLiteIntArray* src)` | c/common.h |
| `TfLiteIntArrayCreate` | `TfLiteIntArray* TfLiteIntArrayCreate(int size)` | c/common.h |
| `TfLiteIntArrayEqual` | `int TfLiteIntArrayEqual(const TfLiteIntArray* a, const TfLiteIntArray* b)` | c/common.h |
| `TfLiteIntArrayEqualsArray` | `int TfLiteIntArrayEqualsArray(const TfLiteIntArray* a, int b_size, const int b_data[])` | c/common.h |
| `TfLiteIntArrayFree` | `void TfLiteIntArrayFree(TfLiteIntArray* a)` | c/common.h |
| `TfLiteIntArrayGetSizeInBytes` | `size_t TfLiteIntArrayGetSizeInBytes(int size)` | c/common.h |
| `TfLiteInterpreterAllocateTensors` | `TfLiteStatus TfLiteInterpreterAllocateTensors( TfLiteInterpreter* interpreter)` | c/c_api.h |
| `TfLiteInterpreterCreate` | `TfLiteInterpreter* TfLiteInterpreterCreate( const TfLiteModel* model, const TfLiteInterpreterOptions* optional_options)` | c/c_api.h |
| `TfLiteInterpreterCreateWithSelectedOps` | `TfLiteInterpreter* TfLiteInterpreterCreateWithSelectedOps(const TfLiteModel* model, const TfLiteInterpreterOptions* options)` | c/c_api_experimental.h |
| `TfLiteInterpreterDelete` | `void TfLiteInterpreterDelete( TfLiteInterpreter* interpreter)` | c/c_api.h |
| `TfLiteInterpreterGetInputTensor` | `TfLiteTensor* TfLiteInterpreterGetInputTensor( const TfLiteInterpreter* interpreter, int32_t input_index)` | c/c_api.h |
| `TfLiteInterpreterGetInputTensorCount` | `int32_t TfLiteInterpreterGetInputTensorCount( const TfLiteInterpreter* interpreter)` | c/c_api.h |
| `TfLiteInterpreterGetInputTensorIndex` | `int32_t TfLiteInterpreterGetInputTensorIndex( const TfLiteInterpreter* interpreter, int32_t input_index)` | c/c_api_experimental.h |
| `TfLiteInterpreterGetOutputTensor` | `const TfLiteTensor* TfLiteInterpreterGetOutputTensor( const TfLiteInterpreter* interpreter, int32_t output_index)` | c/c_api.h |
| `TfLiteInterpreterGetOutputTensorCount` | `int32_t TfLiteInterpreterGetOutputTensorCount( const TfLiteInterpreter* interpreter)` | c/c_api.h |
| `TfLiteInterpreterGetOutputTensorIndex` | `int32_t TfLiteInterpreterGetOutputTensorIndex( const TfLiteInterpreter* interpreter, int32_t output_index)` | c/c_api_experimental.h |
| `TfLiteInterpreterGetSignatureCount` | `int32_t TfLiteInterpreterGetSignatureCount( const TfLiteInterpreter* interpreter)` | c/c_api_experimental.h |
| `TfLiteInterpreterGetSignatureKey` | `const char* TfLiteInterpreterGetSignatureKey( const TfLiteInterpreter* interpreter, int32_t signature_index)` | c/c_api_experimental.h |
| `TfLiteInterpreterGetSignatureRunner` | `TfLiteSignatureRunner* TfLiteInterpreterGetSignatureRunner(const TfLiteInterpreter* interpreter, const char* signature_key)` | c/c_api_experimental.h |
| `TfLiteInterpreterInvoke` | `TfLiteStatus TfLiteInterpreterInvoke( TfLiteInterpreter* interpreter)` | c/c_api.h |
| `TfLiteInterpreterModifyGraphWithDelegate` | `TfLiteStatus TfLiteInterpreterModifyGraphWithDelegate( const TfLiteInterpreter* interpreter, TfLiteDelegate* delegate)` | c/c_api_experimental.h |
| `TfLiteInterpreterOptionsAddBuiltinOp` | `void TfLiteInterpreterOptionsAddBuiltinOp( TfLiteInterpreterOptions* options, TfLiteBuiltinOperator op, const TfLiteRegistration* registration, int32_t min_version, int32_t max_version)` | c/c_api_experimental.h |
| `TfLiteInterpreterOptionsAddCustomOp` | `void TfLiteInterpreterOptionsAddCustomOp( TfLiteInterpreterOptions* options, const char* name, const TfLiteRegistration* registration, int32_t min_version, int32_t max_version)` | c/c_api_experimental.h |
| `TfLiteInterpreterOptionsAddDelegate` | `void TfLiteInterpreterOptionsAddDelegate( TfLiteInterpreterOptions* options, TfLiteDelegate* delegate)` | c/c_api.h |
| `TfLiteInterpreterOptionsAddRegistrationExternal` | `void TfLiteInterpreterOptionsAddRegistrationExternal( TfLiteInterpreterOptions* options, TfLiteRegistrationExternal* registration)` | c/c_api.h |
| `TfLiteInterpreterOptionsCreate` | `TfLiteInterpreterOptions* TfLiteInterpreterOptionsCreate()` | c/c_api.h |
| `TfLiteInterpreterOptionsDelete` | `void TfLiteInterpreterOptionsDelete( TfLiteInterpreterOptions* options)` | c/c_api.h |
| `TfLiteInterpreterOptionsSetEnableDelegateFallback` | `void TfLiteInterpreterOptionsSetEnableDelegateFallback( TfLiteInterpreterOptions* options, bool enable)` | c/c_api_experimental.h |
| `TfLiteInterpreterOptionsSetErrorReporter` | `void TfLiteInterpreterOptionsSetErrorReporter( TfLiteInterpreterOptions* options, void (*reporter)(void* user_data, const char* format, va_list args), void* user_data)` | c/c_api.h |
| `TfLiteInterpreterOptionsSetNumThreads` | `void TfLiteInterpreterOptionsSetNumThreads( TfLiteInterpreterOptions* options, int32_t num_threads)` | c/c_api.h |
| `TfLiteInterpreterOptionsSetOpResolver` | `void TfLiteInterpreterOptionsSetOpResolver( TfLiteInterpreterOptions* options, const TfLiteRegistration* (*find_builtin_op)(void* user_data, TfLiteBuiltinOperator op, int version), const TfLiteRegistration* (*find_custom_op)(void* user_data, const char* custom_op, int version), void* op_resolver_user_data)` | c/c_api_experimental.h |
| `TfLiteInterpreterOptionsSetOpResolverV1` | `void TfLiteInterpreterOptionsSetOpResolverV1( TfLiteInterpreterOptions* options, const TfLiteRegistration_V1* (*find_builtin_op_v1)(void* user_data, TfLiteBuiltinOperator op, int version), const TfLiteRegistration_V1* (*find_custom_op_v1)(void* user_data, const char* op, int version), void* op_resolver_user_data)` | c/c_api_experimental.h |
| `TfLiteInterpreterOptionsSetUseNNAPI` | `void TfLiteInterpreterOptionsSetUseNNAPI( TfLiteInterpreterOptions* options, bool enable)` | c/c_api_experimental.h |
| `TfLiteInterpreterResetVariableTensors` | `TfLiteStatus TfLiteInterpreterResetVariableTensors( TfLiteInterpreter* interpreter)` | c/c_api_experimental.h |
| `TfLiteInterpreterResizeInputTensor` | `TfLiteStatus TfLiteInterpreterResizeInputTensor( TfLiteInterpreter* interpreter, int32_t input_index, const int* input_dims, int32_t input_dims_size)` | c/c_api.h |
| `TfLiteModelCreate` | `TfLiteModel* TfLiteModelCreate(const void* model_data, size_t model_size)` | c/c_api.h |
| `TfLiteModelCreateFromFile` | `TfLiteModel* TfLiteModelCreateFromFile( const char* model_path)` | c/c_api.h |
| `TfLiteModelDelete` | `void TfLiteModelDelete(TfLiteModel* model)` | c/c_api.h |
| `TfLiteQuantizationFree` | `void TfLiteQuantizationFree(TfLiteQuantization* quantization)` | c/common.h |
| `TfLiteRegistrationExternalCreate` | `TfLiteRegistrationExternal* TfLiteRegistrationExternalCreate(const char* custom_name, const int version)` | c/c_api.h |
| `TfLiteRegistrationExternalDelete` | `void TfLiteRegistrationExternalDelete( TfLiteRegistrationExternal* registration)` | c/c_api.h |
| `TfLiteRegistrationExternalSetInvoke` | `void TfLiteRegistrationExternalSetInvoke( TfLiteRegistrationExternal* registration, TfLiteStatus (*invoke)(TfLiteOpaqueContext* context, TfLiteOpaqueNode* node))` | c/c_api.h |
| `TfLiteRegistrationExternalSetPrepare` | `void TfLiteRegistrationExternalSetPrepare( TfLiteRegistrationExternal* registration, TfLiteStatus (*prepare)(TfLiteOpaqueContext* context, TfLiteOpaqueNode* node))` | c/c_api.h |
| `TfLiteSetAllowBufferHandleOutput` | `void TfLiteSetAllowBufferHandleOutput( const TfLiteInterpreter* interpreter, bool allow_buffer_handle_output)` | c/c_api_experimental.h |
| `TfLiteSignatureRunnerAllocateTensors` | `TfLiteStatus TfLiteSignatureRunnerAllocateTensors( TfLiteSignatureRunner* signature_runner)` | c/c_api_experimental.h |
| `TfLiteSignatureRunnerDelete` | `void TfLiteSignatureRunnerDelete( TfLiteSignatureRunner* signature_runner)` | c/c_api_experimental.h |
| `TfLiteSignatureRunnerGetInputCount` | `size_t TfLiteSignatureRunnerGetInputCount( const TfLiteSignatureRunner* signature_runner)` | c/c_api_experimental.h |
| `TfLiteSignatureRunnerGetInputName` | `const char* TfLiteSignatureRunnerGetInputName( const TfLiteSignatureRunner* signature_runner, const int32_t input_index)` | c/c_api_experimental.h |
| `TfLiteSignatureRunnerGetInputTensor` | `TfLiteTensor* TfLiteSignatureRunnerGetInputTensor( TfLiteSignatureRunner* signature_runner, const char* input_name)` | c/c_api_experimental.h |
| `TfLiteSignatureRunnerGetOutputCount` | `size_t TfLiteSignatureRunnerGetOutputCount( const TfLiteSignatureRunner* signature_runner)` | c/c_api_experimental.h |
| `TfLiteSignatureRunnerGetOutputName` | `const char* TfLiteSignatureRunnerGetOutputName( const TfLiteSignatureRunner* signature_runner, int32_t output_index)` | c/c_api_experimental.h |
| `TfLiteSignatureRunnerGetOutputTensor` | `const TfLiteTensor* TfLiteSignatureRunnerGetOutputTensor( const TfLiteSignatureRunner* signature_runner, const char* output_name)` | c/c_api_experimental.h |
| `TfLiteSignatureRunnerInvoke` | `TfLiteStatus TfLiteSignatureRunnerInvoke( TfLiteSignatureRunner* signature_runner)` | c/c_api_experimental.h |
| `TfLiteSignatureRunnerResizeInputTensor` | `TfLiteStatus TfLiteSignatureRunnerResizeInputTensor( TfLiteSignatureRunner* signature_runner, const char* input_name, const int* input_dims, int32_t input_dims_size)` | c/c_api_experimental.h |
| `TfLiteSparsityFree` | `void TfLiteSparsityFree(TfLiteSparsity* sparsity)` | c/common.h |
| `TfLiteTensorByteSize` | `size_t TfLiteTensorByteSize(const TfLiteTensor* tensor)` | c/c_api.h |
| `TfLiteTensorCopy` | `TfLiteStatus TfLiteTensorCopy(const TfLiteTensor* src, TfLiteTensor* dst)` | c/common.h |
| `TfLiteTensorCopyFromBuffer` | `TfLiteStatus TfLiteTensorCopyFromBuffer( TfLiteTensor* tensor, const void* input_data, size_t input_data_size)` | c/c_api.h |
| `TfLiteTensorCopyToBuffer` | `TfLiteStatus TfLiteTensorCopyToBuffer( const TfLiteTensor* output_tensor, void* output_data, size_t output_data_size)` | c/c_api.h |
| `TfLiteTensorData` | `void* TfLiteTensorData(const TfLiteTensor* tensor)` | c/c_api.h |
| `TfLiteTensorDataFree` | `void TfLiteTensorDataFree(TfLiteTensor* t)` | c/common.h |
| `TfLiteTensorDim` | `int32_t TfLiteTensorDim(const TfLiteTensor* tensor, int32_t dim_index)` | c/c_api.h |
| `TfLiteTensorFree` | `void TfLiteTensorFree(TfLiteTensor* t)` | c/common.h |
| `TfLiteTensorName` | `const char* TfLiteTensorName(const TfLiteTensor* tensor)` | c/c_api.h |
| `TfLiteTensorNumDims` | `int32_t TfLiteTensorNumDims(const TfLiteTensor* tensor)` | c/c_api.h |
| `TfLiteTensorQuantizationParams` | `TfLiteQuantizationParams TfLiteTensorQuantizationParams( const TfLiteTensor* tensor)` | c/c_api.h |
| `TfLiteTensorRealloc` | `void TfLiteTensorRealloc(size_t num_bytes, TfLiteTensor* tensor)` | c/common.h |
| `TfLiteTensorReset` | `void TfLiteTensorReset(TfLiteType type, const char* name, TfLiteIntArray* dims, TfLiteQuantizationParams quantization, char* buffer, size_t size, TfLiteAllocationType allocation_type, const void* allocation, bool is_variable, TfLiteTensor* tensor)` | c/common.h |
| `TfLiteTensorType` | `TfLiteType TfLiteTensorType(const TfLiteTensor* tensor)` | c/c_api.h |
| `TfLiteTypeGetName` | `const char* TfLiteTypeGetName(TfLiteType type)` | c/common.h |
| `TfLiteVersion` | `const char* TfLiteVersion(void)` | c/c_api.h |

## 6. `libpython3.13.so.1.0` — C API (только для кода на C)

Экспорт — 1474 функций (1023 открытых `Py*`, 451 служебных `_Py*`). Их вызывает logix; скрипту на Python они не нужны и недоступны. Открытые — по группам префикса:

- **Py** (67): `Py_AddPendingCall`, `Py_AtExit`, `Py_BuildValue`, `Py_BytesMain`, `Py_CompileString`, `Py_CompileStringExFlags`, `Py_CompileStringFlags`, `Py_CompileStringObject`, `Py_DecRef`, `Py_DecodeLocale`, `Py_EncodeLocale`, `Py_EndInterpreter`, `Py_EnterRecursiveCall`, `Py_Exit`, `Py_ExitStatusException`, `Py_FatalError`, `Py_FdIsInteractive`, `Py_Finalize`, `Py_FinalizeEx`, `Py_FrozenMain`, `Py_GETENV`, `Py_GenericAlias`, `Py_GetArgcArgv`, `Py_GetBuildInfo`, `Py_GetCompiler`, `Py_GetConstant`, `Py_GetConstantBorrowed`, `Py_GetCopyright`, `Py_GetExecPrefix`, `Py_GetPath`, `Py_GetPlatform`, `Py_GetPrefix`, `Py_GetProgramFullPath`, `Py_GetProgramName`, `Py_GetPythonHome`, `Py_GetRecursionLimit`, `Py_GetVersion`, `Py_HashPointer`, `Py_IncRef`, `Py_Initialize`, `Py_InitializeEx`, `Py_InitializeFromConfig`, `Py_Is`, `Py_IsFalse`, `Py_IsFinalizing`, `Py_IsInitialized`, `Py_IsNone`, `Py_IsTrue`, `Py_LeaveRecursiveCall`, `Py_Main`, `Py_MakePendingCalls`, `Py_NewInterpreter`, `Py_NewInterpreterFromConfig`, `Py_NewRef`, `Py_PreInitialize`, `Py_PreInitializeFromArgs`, `Py_PreInitializeFromBytesArgs`, `Py_ReprEnter`, `Py_ReprLeave`, `Py_RunMain`, `Py_SetPath`, `Py_SetProgramName`, `Py_SetPythonHome`, `Py_SetRecursionLimit`, `Py_UniversalNewlineFgets`, `Py_VaBuildValue`, `Py_XNewRef`
- **PyAIter** (1): `PyAIter_Check`
- **PyArg** (7): `PyArg_Parse`, `PyArg_ParseTuple`, `PyArg_ParseTupleAndKeywords`, `PyArg_UnpackTuple`, `PyArg_VaParse`, `PyArg_VaParseTupleAndKeywords`, `PyArg_ValidateKeywordArguments`
- **PyAsyncGen** (1): `PyAsyncGen_New`
- **PyBool** (1): `PyBool_FromLong`
- **PyBuffer** (8): `PyBuffer_FillContiguousStrides`, `PyBuffer_FillInfo`, `PyBuffer_FromContiguous`, `PyBuffer_GetPointer`, `PyBuffer_IsContiguous`, `PyBuffer_Release`, `PyBuffer_SizeFromFormat`, `PyBuffer_ToContiguous`
- **PyByteArray** (6): `PyByteArray_AsString`, `PyByteArray_Concat`, `PyByteArray_FromObject`, `PyByteArray_FromStringAndSize`, `PyByteArray_Resize`, `PyByteArray_Size`
- **PyBytes** (12): `PyBytes_AsString`, `PyBytes_AsStringAndSize`, `PyBytes_Concat`, `PyBytes_ConcatAndDel`, `PyBytes_DecodeEscape`, `PyBytes_FromFormat`, `PyBytes_FromFormatV`, `PyBytes_FromObject`, `PyBytes_FromString`, `PyBytes_FromStringAndSize`, `PyBytes_Repr`, `PyBytes_Size`
- **PyCFunction** (6): `PyCFunction_Call`, `PyCFunction_GetFlags`, `PyCFunction_GetFunction`, `PyCFunction_GetSelf`, `PyCFunction_New`, `PyCFunction_NewEx`
- **PyCMethod** (1): `PyCMethod_New`
- **PyCallIter** (1): `PyCallIter_New`
- **PyCallable** (1): `PyCallable_Check`
- **PyCapsule** (11): `PyCapsule_GetContext`, `PyCapsule_GetDestructor`, `PyCapsule_GetName`, `PyCapsule_GetPointer`, `PyCapsule_Import`, `PyCapsule_IsValid`, `PyCapsule_New`, `PyCapsule_SetContext`, `PyCapsule_SetDestructor`, `PyCapsule_SetName`, `PyCapsule_SetPointer`
- **PyCell** (3): `PyCell_Get`, `PyCell_New`, `PyCell_Set`
- **PyClassMethod** (1): `PyClassMethod_New`
- **PyCode** (10): `PyCode_AddWatcher`, `PyCode_Addr2Line`, `PyCode_Addr2Location`, `PyCode_ClearWatcher`, `PyCode_GetCellvars`, `PyCode_GetCode`, `PyCode_GetFreevars`, `PyCode_GetVarnames`, `PyCode_NewEmpty`, `PyCode_Optimize`
- **PyCodec** (19): `PyCodec_BackslashReplaceErrors`, `PyCodec_Decode`, `PyCodec_Decoder`, `PyCodec_Encode`, `PyCodec_Encoder`, `PyCodec_IgnoreErrors`, `PyCodec_IncrementalDecoder`, `PyCodec_IncrementalEncoder`, `PyCodec_KnownEncoding`, `PyCodec_LookupError`, `PyCodec_NameReplaceErrors`, `PyCodec_Register`, `PyCodec_RegisterError`, `PyCodec_ReplaceErrors`, `PyCodec_StreamReader`, `PyCodec_StreamWriter`, `PyCodec_StrictErrors`, `PyCodec_Unregister`, `PyCodec_XMLCharRefReplaceErrors`
- **PyCompile** (2): `PyCompile_OpcodeStackEffect`, `PyCompile_OpcodeStackEffectWithJump`
- **PyComplex** (5): `PyComplex_AsCComplex`, `PyComplex_FromCComplex`, `PyComplex_FromDoubles`, `PyComplex_ImagAsDouble`, `PyComplex_RealAsDouble`
- **PyConfig** (9): `PyConfig_Clear`, `PyConfig_InitIsolatedConfig`, `PyConfig_InitPythonConfig`, `PyConfig_Read`, `PyConfig_SetArgv`, `PyConfig_SetBytesArgv`, `PyConfig_SetBytesString`, `PyConfig_SetString`, `PyConfig_SetWideStringList`
- **PyContext** (5): `PyContext_Copy`, `PyContext_CopyCurrent`, `PyContext_Enter`, `PyContext_Exit`, `PyContext_New`
- **PyContextVar** (4): `PyContextVar_Get`, `PyContextVar_New`, `PyContextVar_Reset`, `PyContextVar_Set`
- **PyCoro** (1): `PyCoro_New`
- **PyCriticalSection** (2): `PyCriticalSection_Begin`, `PyCriticalSection_End`
- **PyCriticalSection2** (2): `PyCriticalSection2_Begin`, `PyCriticalSection2_End`
- **PyDescr** (6): `PyDescr_IsData`, `PyDescr_NewClassMethod`, `PyDescr_NewGetSet`, `PyDescr_NewMember`, `PyDescr_NewMethod`, `PyDescr_NewWrapper`
- **PyDict** (30): `PyDict_AddWatcher`, `PyDict_Clear`, `PyDict_ClearWatcher`, `PyDict_Contains`, `PyDict_ContainsString`, `PyDict_Copy`, `PyDict_DelItem`, `PyDict_DelItemString`, `PyDict_GetItem`, `PyDict_GetItemRef`, `PyDict_GetItemString`, `PyDict_GetItemStringRef`, `PyDict_GetItemWithError`, `PyDict_Items`, `PyDict_Keys`, `PyDict_Merge`, `PyDict_MergeFromSeq2`, `PyDict_New`, `PyDict_Next`, `PyDict_Pop`, `PyDict_PopString`, `PyDict_SetDefault`, `PyDict_SetDefaultRef`, `PyDict_SetItem`, `PyDict_SetItemString`, `PyDict_Size`, `PyDict_Unwatch`, `PyDict_Update`, `PyDict_Values`, `PyDict_Watch`
- **PyDictProxy** (1): `PyDictProxy_New`
- **PyErr** (50): `PyErr_BadArgument`, `PyErr_BadInternalCall`, `PyErr_CheckSignals`, `PyErr_Clear`, `PyErr_Display`, `PyErr_DisplayException`, `PyErr_ExceptionMatches`, `PyErr_Fetch`, `PyErr_Format`, `PyErr_FormatUnraisable`, `PyErr_FormatV`, `PyErr_GetExcInfo`, `PyErr_GetHandledException`, `PyErr_GetRaisedException`, `PyErr_GivenExceptionMatches`, `PyErr_NewException`, `PyErr_NewExceptionWithDoc`, `PyErr_NoMemory`, `PyErr_NormalizeException`, `PyErr_Occurred`, `PyErr_Print`, `PyErr_PrintEx`, `PyErr_ProgramText`, `PyErr_ProgramTextObject`, `PyErr_RangedSyntaxLocationObject`, `PyErr_ResourceWarning`, `PyErr_Restore`, `PyErr_SetExcInfo`, `PyErr_SetFromErrno`, `PyErr_SetFromErrnoWithFilename`, `PyErr_SetFromErrnoWithFilenameObject`, `PyErr_SetFromErrnoWithFilenameObjects`, `PyErr_SetHandledException`, `PyErr_SetImportError`, `PyErr_SetImportErrorSubclass`, `PyErr_SetInterrupt`, `PyErr_SetInterruptEx`, `PyErr_SetNone`, `PyErr_SetObject`, `PyErr_SetRaisedException`, `PyErr_SetString`, `PyErr_SyntaxLocation`, `PyErr_SyntaxLocationEx`, `PyErr_SyntaxLocationObject`, `PyErr_WarnEx`, `PyErr_WarnExplicit`, `PyErr_WarnExplicitFormat`, `PyErr_WarnExplicitObject`, `PyErr_WarnFormat`, `PyErr_WriteUnraisable`
- **PyEval** (29): `PyEval_AcquireLock`, `PyEval_AcquireThread`, `PyEval_CallFunction`, `PyEval_CallMethod`, `PyEval_CallObjectWithKeywords`, `PyEval_EvalCode`, `PyEval_EvalCodeEx`, `PyEval_EvalFrame`, `PyEval_EvalFrameEx`, `PyEval_GetBuiltins`, `PyEval_GetFrame`, `PyEval_GetFrameBuiltins`, `PyEval_GetFrameGlobals`, `PyEval_GetFrameLocals`, `PyEval_GetFuncDesc`, `PyEval_GetFuncName`, `PyEval_GetGlobals`, `PyEval_GetLocals`, `PyEval_InitThreads`, `PyEval_MergeCompilerFlags`, `PyEval_ReleaseLock`, `PyEval_ReleaseThread`, `PyEval_RestoreThread`, `PyEval_SaveThread`, `PyEval_SetProfile`, `PyEval_SetProfileAllThreads`, `PyEval_SetTrace`, `PyEval_SetTraceAllThreads`, `PyEval_ThreadsInitialized`
- **PyEvent** (2): `PyEvent_Wait`, `PyEvent_WaitTimed`
- **PyException** (8): `PyException_GetArgs`, `PyException_GetCause`, `PyException_GetContext`, `PyException_GetTraceback`, `PyException_SetArgs`, `PyException_SetCause`, `PyException_SetContext`, `PyException_SetTraceback`
- **PyExceptionClass** (1): `PyExceptionClass_Name`
- **PyFile** (8): `PyFile_FromFd`, `PyFile_GetLine`, `PyFile_NewStdPrinter`, `PyFile_OpenCode`, `PyFile_OpenCodeObject`, `PyFile_SetOpenCodeHook`, `PyFile_WriteObject`, `PyFile_WriteString`
- **PyFloat** (12): `PyFloat_AsDouble`, `PyFloat_FromDouble`, `PyFloat_FromString`, `PyFloat_GetInfo`, `PyFloat_GetMax`, `PyFloat_GetMin`, `PyFloat_Pack2`, `PyFloat_Pack4`, `PyFloat_Pack8`, `PyFloat_Unpack2`, `PyFloat_Unpack4`, `PyFloat_Unpack8`
- **PyFrame** (14): `PyFrame_FastToLocals`, `PyFrame_FastToLocalsWithError`, `PyFrame_GetBack`, `PyFrame_GetBuiltins`, `PyFrame_GetCode`, `PyFrame_GetGenerator`, `PyFrame_GetGlobals`, `PyFrame_GetLasti`, `PyFrame_GetLineNumber`, `PyFrame_GetLocals`, `PyFrame_GetVar`, `PyFrame_GetVarString`, `PyFrame_LocalsToFast`, `PyFrame_New`
- **PyFrozenSet** (1): `PyFrozenSet_New`
- **PyFunction** (16): `PyFunction_AddWatcher`, `PyFunction_ClearWatcher`, `PyFunction_GetAnnotations`, `PyFunction_GetClosure`, `PyFunction_GetCode`, `PyFunction_GetDefaults`, `PyFunction_GetGlobals`, `PyFunction_GetKwDefaults`, `PyFunction_GetModule`, `PyFunction_New`, `PyFunction_NewWithQualName`, `PyFunction_SetAnnotations`, `PyFunction_SetClosure`, `PyFunction_SetDefaults`, `PyFunction_SetKwDefaults`, `PyFunction_SetVectorcall`
- **PyGC** (4): `PyGC_Collect`, `PyGC_Disable`, `PyGC_Enable`, `PyGC_IsEnabled`
- **PyGILState** (4): `PyGILState_Check`, `PyGILState_Ensure`, `PyGILState_GetThisThreadState`, `PyGILState_Release`
- **PyGen** (3): `PyGen_GetCode`, `PyGen_New`, `PyGen_NewWithQualName`
- **PyHash** (1): `PyHash_GetFuncDef`
- **PyImport** (22): `PyImport_AddModule`, `PyImport_AddModuleObject`, `PyImport_AddModuleRef`, `PyImport_AppendInittab`, `PyImport_ExecCodeModule`, `PyImport_ExecCodeModuleEx`, `PyImport_ExecCodeModuleObject`, `PyImport_ExecCodeModuleWithPathnames`, `PyImport_ExtendInittab`, `PyImport_GetImporter`, `PyImport_GetMagicNumber`, `PyImport_GetMagicTag`, `PyImport_GetModule`, `PyImport_GetModuleDict`, `PyImport_Import`, `PyImport_ImportFrozenModule`, `PyImport_ImportFrozenModuleObject`, `PyImport_ImportModule`, `PyImport_ImportModuleLevel`, `PyImport_ImportModuleLevelObject`, `PyImport_ImportModuleNoBlock`, `PyImport_ReloadModule`
- **PyIndex** (1): `PyIndex_Check`
- **PyInit** (29): `PyInit__abc`, `PyInit__ast`, `PyInit__codecs`, `PyInit__collections`, `PyInit__functools`, `PyInit__imp`, `PyInit__io`, `PyInit__locale`, `PyInit__operator`, `PyInit__signal`, `PyInit__sre`, `PyInit__stat`, `PyInit__string`, `PyInit__suggestions`, `PyInit__symtable`, `PyInit__sysconfig`, `PyInit__thread`, `PyInit__tokenize`, `PyInit__tracemalloc`, `PyInit__typing`, `PyInit__weakref`, `PyInit_atexit`, `PyInit_errno`, `PyInit_faulthandler`, `PyInit_gc`, `PyInit_itertools`, `PyInit_posix`, `PyInit_pwd`, `PyInit_time`
- **PyInstanceMethod** (2): `PyInstanceMethod_Function`, `PyInstanceMethod_New`
- **PyInterpreterState** (10): `PyInterpreterState_Clear`, `PyInterpreterState_Delete`, `PyInterpreterState_Get`, `PyInterpreterState_GetDict`, `PyInterpreterState_GetID`, `PyInterpreterState_Head`, `PyInterpreterState_Main`, `PyInterpreterState_New`, `PyInterpreterState_Next`, `PyInterpreterState_ThreadHead`
- **PyIter** (3): `PyIter_Check`, `PyIter_Next`, `PyIter_Send`
- **PyList** (14): `PyList_Append`, `PyList_AsTuple`, `PyList_Clear`, `PyList_Extend`, `PyList_GetItem`, `PyList_GetItemRef`, `PyList_GetSlice`, `PyList_Insert`, `PyList_New`, `PyList_Reverse`, `PyList_SetItem`, `PyList_SetSlice`, `PyList_Size`, `PyList_Sort`
- **PyLong** (27): `PyLong_AsDouble`, `PyLong_AsInt`, `PyLong_AsLong`, `PyLong_AsLongAndOverflow`, `PyLong_AsLongLong`, `PyLong_AsLongLongAndOverflow`, `PyLong_AsNativeBytes`, `PyLong_AsSize_t`, `PyLong_AsSsize_t`, `PyLong_AsUnsignedLong`, `PyLong_AsUnsignedLongLong`, `PyLong_AsUnsignedLongLongMask`, `PyLong_AsUnsignedLongMask`, `PyLong_AsVoidPtr`, `PyLong_FromDouble`, `PyLong_FromLong`, `PyLong_FromLongLong`, `PyLong_FromNativeBytes`, `PyLong_FromSize_t`, `PyLong_FromSsize_t`, `PyLong_FromString`, `PyLong_FromUnicodeObject`, `PyLong_FromUnsignedLong`, `PyLong_FromUnsignedLongLong`, `PyLong_FromUnsignedNativeBytes`, `PyLong_FromVoidPtr`, `PyLong_GetInfo`
- **PyMapping** (14): `PyMapping_Check`, `PyMapping_GetItemString`, `PyMapping_GetOptionalItem`, `PyMapping_GetOptionalItemString`, `PyMapping_HasKey`, `PyMapping_HasKeyString`, `PyMapping_HasKeyStringWithError`, `PyMapping_HasKeyWithError`, `PyMapping_Items`, `PyMapping_Keys`, `PyMapping_Length`, `PyMapping_SetItemString`, `PyMapping_Size`, `PyMapping_Values`
- **PyMarshal** (9): `PyMarshal_Init`, `PyMarshal_ReadLastObjectFromFile`, `PyMarshal_ReadLongFromFile`, `PyMarshal_ReadObjectFromFile`, `PyMarshal_ReadObjectFromString`, `PyMarshal_ReadShortFromFile`, `PyMarshal_WriteLongToFile`, `PyMarshal_WriteObjectToFile`, `PyMarshal_WriteObjectToString`
- **PyMem** (11): `PyMem_Calloc`, `PyMem_Free`, `PyMem_GetAllocator`, `PyMem_Malloc`, `PyMem_RawCalloc`, `PyMem_RawFree`, `PyMem_RawMalloc`, `PyMem_RawRealloc`, `PyMem_Realloc`, `PyMem_SetAllocator`, `PyMem_SetupDebugHooks`
- **PyMember** (2): `PyMember_GetOne`, `PyMember_SetOne`
- **PyMemoryView** (4): `PyMemoryView_FromBuffer`, `PyMemoryView_FromMemory`, `PyMemoryView_FromObject`, `PyMemoryView_GetContiguous`
- **PyMethod** (3): `PyMethod_Function`, `PyMethod_New`, `PyMethod_Self`
- **PyModule** (20): `PyModule_Add`, `PyModule_AddFunctions`, `PyModule_AddIntConstant`, `PyModule_AddObject`, `PyModule_AddObjectRef`, `PyModule_AddStringConstant`, `PyModule_AddType`, `PyModule_Create2`, `PyModule_ExecDef`, `PyModule_FromDefAndSpec2`, `PyModule_GetDef`, `PyModule_GetDict`, `PyModule_GetFilename`, `PyModule_GetFilenameObject`, `PyModule_GetName`, `PyModule_GetNameObject`, `PyModule_GetState`, `PyModule_New`, `PyModule_NewObject`, `PyModule_SetDocString`
- **PyModuleDef** (1): `PyModuleDef_Init`
- **PyMonitoring** (2): `PyMonitoring_EnterScope`, `PyMonitoring_ExitScope`
- **PyMutex** (2): `PyMutex_Lock`, `PyMutex_Unlock`
- **PyNumber** (37): `PyNumber_Absolute`, `PyNumber_Add`, `PyNumber_And`, `PyNumber_AsSsize_t`, `PyNumber_Check`, `PyNumber_Divmod`, `PyNumber_Float`, `PyNumber_FloorDivide`, `PyNumber_InPlaceAdd`, `PyNumber_InPlaceAnd`, `PyNumber_InPlaceFloorDivide`, `PyNumber_InPlaceLshift`, `PyNumber_InPlaceMatrixMultiply`, `PyNumber_InPlaceMultiply`, `PyNumber_InPlaceOr`, `PyNumber_InPlacePower`, `PyNumber_InPlaceRemainder`, `PyNumber_InPlaceRshift`, `PyNumber_InPlaceSubtract`, `PyNumber_InPlaceTrueDivide`, `PyNumber_InPlaceXor`, `PyNumber_Index`, `PyNumber_Invert`, `PyNumber_Long`, `PyNumber_Lshift`, `PyNumber_MatrixMultiply`, `PyNumber_Multiply`, `PyNumber_Negative`, `PyNumber_Or`, `PyNumber_Positive`, `PyNumber_Power`, `PyNumber_Remainder`, `PyNumber_Rshift`, `PyNumber_Subtract`, `PyNumber_ToBase`, `PyNumber_TrueDivide`, `PyNumber_Xor`
- **PyODict** (3): `PyODict_DelItem`, `PyODict_New`, `PyODict_SetItem`
- **PyOS** (17): `PyOS_AfterFork`, `PyOS_AfterFork_Child`, `PyOS_AfterFork_Parent`, `PyOS_BeforeFork`, `PyOS_FSPath`, `PyOS_InterruptOccurred`, `PyOS_Readline`, `PyOS_double_to_string`, `PyOS_getsig`, `PyOS_mystricmp`, `PyOS_mystrnicmp`, `PyOS_setsig`, `PyOS_snprintf`, `PyOS_string_to_double`, `PyOS_strtol`, `PyOS_strtoul`, `PyOS_vsnprintf`
- **PyObject** (84): `PyObject_ASCII`, `PyObject_AsCharBuffer`, `PyObject_AsFileDescriptor`, `PyObject_AsReadBuffer`, `PyObject_AsWriteBuffer`, `PyObject_Bytes`, `PyObject_Call`, `PyObject_CallFinalizer`, `PyObject_CallFinalizerFromDealloc`, `PyObject_CallFunction`, `PyObject_CallFunctionObjArgs`, `PyObject_CallMethod`, `PyObject_CallMethodObjArgs`, `PyObject_CallNoArgs`, `PyObject_CallObject`, `PyObject_CallOneArg`, `PyObject_Calloc`, `PyObject_CheckBuffer`, `PyObject_CheckReadBuffer`, `PyObject_ClearManagedDict`, `PyObject_ClearWeakRefs`, `PyObject_CopyData`, `PyObject_DelAttr`, `PyObject_DelAttrString`, `PyObject_DelItem`, `PyObject_DelItemString`, `PyObject_Dir`, `PyObject_Format`, `PyObject_Free`, `PyObject_GC_Del`, `PyObject_GC_IsFinalized`, `PyObject_GC_IsTracked`, `PyObject_GC_Track`, `PyObject_GC_UnTrack`, `PyObject_GET_WEAKREFS_LISTPTR`, `PyObject_GenericGetAttr`, `PyObject_GenericGetDict`, `PyObject_GenericHash`, `PyObject_GenericSetAttr`, `PyObject_GenericSetDict`, `PyObject_GetAIter`, `PyObject_GetArenaAllocator`, `PyObject_GetAttr`, `PyObject_GetAttrString`, `PyObject_GetBuffer`, `PyObject_GetItem`, `PyObject_GetItemData`, `PyObject_GetIter`, `PyObject_GetOptionalAttr`, `PyObject_GetOptionalAttrString`, `PyObject_GetTypeData`, `PyObject_HasAttr`, `PyObject_HasAttrString`, `PyObject_HasAttrStringWithError`, `PyObject_HasAttrWithError`, `PyObject_Hash`, `PyObject_HashNotImplemented`, `PyObject_IS_GC`, `PyObject_Init`, `PyObject_InitVar`, `PyObject_IsInstance`, `PyObject_IsSubclass`, `PyObject_IsTrue`, `PyObject_Length`, `PyObject_LengthHint`, `PyObject_Malloc`, `PyObject_Not`, `PyObject_Print`, `PyObject_Realloc`, `PyObject_Repr`, `PyObject_RichCompare`, `PyObject_RichCompareBool`, `PyObject_SelfIter`, `PyObject_SetArenaAllocator`, `PyObject_SetAttr`, `PyObject_SetAttrString`, `PyObject_SetItem`, `PyObject_Size`, `PyObject_Str`, `PyObject_Type`, `PyObject_Vectorcall`, `PyObject_VectorcallDict`, `PyObject_VectorcallMethod`, `PyObject_VisitManagedDict`
- **PyPickleBuffer** (3): `PyPickleBuffer_FromObject`, `PyPickleBuffer_GetBuffer`, `PyPickleBuffer_Release`
- **PyPreConfig** (2): `PyPreConfig_InitIsolatedConfig`, `PyPreConfig_InitPythonConfig`
- **PyRefTracer** (2): `PyRefTracer_GetTracer`, `PyRefTracer_SetTracer`
- **PyRun** (20): `PyRun_AnyFile`, `PyRun_AnyFileEx`, `PyRun_AnyFileExFlags`, `PyRun_AnyFileFlags`, `PyRun_File`, `PyRun_FileEx`, `PyRun_FileExFlags`, `PyRun_FileFlags`, `PyRun_InteractiveLoop`, `PyRun_InteractiveLoopFlags`, `PyRun_InteractiveOne`, `PyRun_InteractiveOneFlags`, `PyRun_InteractiveOneObject`, `PyRun_SimpleFile`, `PyRun_SimpleFileEx`, `PyRun_SimpleFileExFlags`, `PyRun_SimpleString`, `PyRun_SimpleStringFlags`, `PyRun_String`, `PyRun_StringFlags`
- **PySeqIter** (1): `PySeqIter_New`
- **PySequence** (20): `PySequence_Check`, `PySequence_Concat`, `PySequence_Contains`, `PySequence_Count`, `PySequence_DelItem`, `PySequence_DelSlice`, `PySequence_Fast`, `PySequence_GetItem`, `PySequence_GetSlice`, `PySequence_In`, `PySequence_InPlaceConcat`, `PySequence_InPlaceRepeat`, `PySequence_Index`, `PySequence_Length`, `PySequence_List`, `PySequence_Repeat`, `PySequence_SetItem`, `PySequence_SetSlice`, `PySequence_Size`, `PySequence_Tuple`
- **PySet** (7): `PySet_Add`, `PySet_Clear`, `PySet_Contains`, `PySet_Discard`, `PySet_New`, `PySet_Pop`, `PySet_Size`
- **PySignal** (1): `PySignal_SetWakeupFd`
- **PySlice** (5): `PySlice_AdjustIndices`, `PySlice_GetIndices`, `PySlice_GetIndicesEx`, `PySlice_New`, `PySlice_Unpack`
- **PyState** (3): `PyState_AddModule`, `PyState_FindModule`, `PyState_RemoveModule`
- **PyStaticMethod** (1): `PyStaticMethod_New`
- **PyStatus** (7): `PyStatus_Error`, `PyStatus_Exception`, `PyStatus_Exit`, `PyStatus_IsError`, `PyStatus_IsExit`, `PyStatus_NoMemory`, `PyStatus_Ok`
- **PyStructSequence** (6): `PyStructSequence_GetItem`, `PyStructSequence_InitType`, `PyStructSequence_InitType2`, `PyStructSequence_New`, `PyStructSequence_NewType`, `PyStructSequence_SetItem`
- **PySys** (18): `PySys_AddAuditHook`, `PySys_AddWarnOption`, `PySys_AddWarnOptionUnicode`, `PySys_AddXOption`, `PySys_Audit`, `PySys_AuditTuple`, `PySys_FormatStderr`, `PySys_FormatStdout`, `PySys_GetObject`, `PySys_GetXOptions`, `PySys_HasWarnOptions`, `PySys_ResetWarnOptions`, `PySys_SetArgv`, `PySys_SetArgvEx`, `PySys_SetObject`, `PySys_SetPath`, `PySys_WriteStderr`, `PySys_WriteStdout`
- **PyThread** (32): `PyThread_GetInfo`, `PyThread_ParseTimeoutArg`, `PyThread_ReInitTLS`, `PyThread_acquire_lock`, `PyThread_acquire_lock_timed`, `PyThread_acquire_lock_timed_with_retries`, `PyThread_allocate_lock`, `PyThread_create_key`, `PyThread_delete_key`, `PyThread_delete_key_value`, `PyThread_detach_thread`, `PyThread_exit_thread`, `PyThread_free_lock`, `PyThread_get_key_value`, `PyThread_get_stacksize`, `PyThread_get_thread_ident`, `PyThread_get_thread_ident_ex`, `PyThread_get_thread_native_id`, `PyThread_init_thread`, `PyThread_join_thread`, `PyThread_release_lock`, `PyThread_set_key_value`, `PyThread_set_stacksize`, `PyThread_start_joinable_thread`, `PyThread_start_new_thread`, `PyThread_tss_alloc`, `PyThread_tss_create`, `PyThread_tss_delete`, `PyThread_tss_free`, `PyThread_tss_get`, `PyThread_tss_is_created`, `PyThread_tss_set`
- **PyThreadState** (15): `PyThreadState_Clear`, `PyThreadState_Delete`, `PyThreadState_DeleteCurrent`, `PyThreadState_EnterTracing`, `PyThreadState_Get`, `PyThreadState_GetDict`, `PyThreadState_GetFrame`, `PyThreadState_GetID`, `PyThreadState_GetInterpreter`, `PyThreadState_GetUnchecked`, `PyThreadState_LeaveTracing`, `PyThreadState_New`, `PyThreadState_Next`, `PyThreadState_SetAsyncExc`, `PyThreadState_Swap`
- **PyTime** (7): `PyTime_AsSecondsDouble`, `PyTime_Monotonic`, `PyTime_MonotonicRaw`, `PyTime_PerfCounter`, `PyTime_PerfCounterRaw`, `PyTime_Time`, `PyTime_TimeRaw`
- **PyTraceBack** (2): `PyTraceBack_Here`, `PyTraceBack_Print`
- **PyTraceMalloc** (2): `PyTraceMalloc_Track`, `PyTraceMalloc_Untrack`
- **PyTuple** (6): `PyTuple_GetItem`, `PyTuple_GetSlice`, `PyTuple_New`, `PyTuple_Pack`, `PyTuple_SetItem`, `PyTuple_Size`
- **PyType** (26): `PyType_AddWatcher`, `PyType_ClearCache`, `PyType_ClearWatcher`, `PyType_FromMetaclass`, `PyType_FromModuleAndSpec`, `PyType_FromSpec`, `PyType_FromSpecWithBases`, `PyType_GenericAlloc`, `PyType_GenericNew`, `PyType_GetDict`, `PyType_GetFlags`, `PyType_GetFullyQualifiedName`, `PyType_GetModule`, `PyType_GetModuleByDef`, `PyType_GetModuleName`, `PyType_GetModuleState`, `PyType_GetName`, `PyType_GetQualName`, `PyType_GetSlot`, `PyType_GetTypeDataSize`, `PyType_IsSubtype`, `PyType_Modified`, `PyType_Ready`, `PyType_SUPPORTS_WEAKREFS`, `PyType_Unwatch`, `PyType_Watch`
- **PyUnicode** (87): `PyUnicode_Append`, `PyUnicode_AppendAndDel`, `PyUnicode_AsASCIIString`, `PyUnicode_AsCharmapString`, `PyUnicode_AsDecodedObject`, `PyUnicode_AsDecodedUnicode`, `PyUnicode_AsEncodedObject`, `PyUnicode_AsEncodedString`, `PyUnicode_AsEncodedUnicode`, `PyUnicode_AsLatin1String`, `PyUnicode_AsRawUnicodeEscapeString`, `PyUnicode_AsUCS4`, `PyUnicode_AsUCS4Copy`, `PyUnicode_AsUTF16String`, `PyUnicode_AsUTF32String`, `PyUnicode_AsUTF8`, `PyUnicode_AsUTF8AndSize`, `PyUnicode_AsUTF8String`, `PyUnicode_AsUnicodeEscapeString`, `PyUnicode_AsWideChar`, `PyUnicode_AsWideCharString`, `PyUnicode_BuildEncodingMap`, `PyUnicode_Compare`, `PyUnicode_CompareWithASCIIString`, `PyUnicode_Concat`, `PyUnicode_Contains`, `PyUnicode_CopyCharacters`, `PyUnicode_Count`, `PyUnicode_Decode`, `PyUnicode_DecodeASCII`, `PyUnicode_DecodeCharmap`, `PyUnicode_DecodeFSDefault`, `PyUnicode_DecodeFSDefaultAndSize`, `PyUnicode_DecodeLatin1`, `PyUnicode_DecodeLocale`, `PyUnicode_DecodeLocaleAndSize`, `PyUnicode_DecodeRawUnicodeEscape`, `PyUnicode_DecodeUTF16`, `PyUnicode_DecodeUTF16Stateful`, `PyUnicode_DecodeUTF32`, `PyUnicode_DecodeUTF32Stateful`, `PyUnicode_DecodeUTF7`, `PyUnicode_DecodeUTF7Stateful`, `PyUnicode_DecodeUTF8`, `PyUnicode_DecodeUTF8Stateful`, `PyUnicode_DecodeUnicodeEscape`, `PyUnicode_EncodeFSDefault`, `PyUnicode_EncodeLocale`, `PyUnicode_EqualToUTF8`, `PyUnicode_EqualToUTF8AndSize`, `PyUnicode_FSConverter`, `PyUnicode_FSDecoder`, `PyUnicode_Fill`, `PyUnicode_Find`, `PyUnicode_FindChar`, `PyUnicode_Format`, `PyUnicode_FromEncodedObject`, `PyUnicode_FromFormat`, `PyUnicode_FromFormatV`, `PyUnicode_FromKindAndData`, `PyUnicode_FromObject`, `PyUnicode_FromOrdinal`, `PyUnicode_FromString`, `PyUnicode_FromStringAndSize`, `PyUnicode_FromWideChar`, `PyUnicode_GetDefaultEncoding`, `PyUnicode_GetLength`, `PyUnicode_GetSize`, `PyUnicode_InternFromString`, `PyUnicode_InternImmortal`, `PyUnicode_InternInPlace`, `PyUnicode_IsIdentifier`, `PyUnicode_Join`, `PyUnicode_New`, `PyUnicode_Partition`, `PyUnicode_RPartition`, `PyUnicode_RSplit`, `PyUnicode_ReadChar`, `PyUnicode_Replace`, `PyUnicode_Resize`, `PyUnicode_RichCompare`, `PyUnicode_Split`, `PyUnicode_Splitlines`, `PyUnicode_Substring`, `PyUnicode_Tailmatch`, `PyUnicode_Translate`, `PyUnicode_WriteChar`
- **PyUnicodeDecodeError** (9): `PyUnicodeDecodeError_Create`, `PyUnicodeDecodeError_GetEncoding`, `PyUnicodeDecodeError_GetEnd`, `PyUnicodeDecodeError_GetObject`, `PyUnicodeDecodeError_GetReason`, `PyUnicodeDecodeError_GetStart`, `PyUnicodeDecodeError_SetEnd`, `PyUnicodeDecodeError_SetReason`, `PyUnicodeDecodeError_SetStart`
- **PyUnicodeEncodeError** (8): `PyUnicodeEncodeError_GetEncoding`, `PyUnicodeEncodeError_GetEnd`, `PyUnicodeEncodeError_GetObject`, `PyUnicodeEncodeError_GetReason`, `PyUnicodeEncodeError_GetStart`, `PyUnicodeEncodeError_SetEnd`, `PyUnicodeEncodeError_SetReason`, `PyUnicodeEncodeError_SetStart`
- **PyUnicodeTranslateError** (7): `PyUnicodeTranslateError_GetEnd`, `PyUnicodeTranslateError_GetObject`, `PyUnicodeTranslateError_GetReason`, `PyUnicodeTranslateError_GetStart`, `PyUnicodeTranslateError_SetEnd`, `PyUnicodeTranslateError_SetReason`, `PyUnicodeTranslateError_SetStart`
- **PyUnstable** (23): `PyUnstable_AtExit`, `PyUnstable_Code_GetExtra`, `PyUnstable_Code_New`, `PyUnstable_Code_NewWithPosOnlyArgs`, `PyUnstable_Code_SetExtra`, `PyUnstable_CopyPerfMapFile`, `PyUnstable_Eval_RequestCodeExtraIndex`, `PyUnstable_Exc_PrepReraiseStar`, `PyUnstable_GC_VisitObjects`, `PyUnstable_InterpreterFrame_GetCode`, `PyUnstable_InterpreterFrame_GetLasti`, `PyUnstable_InterpreterFrame_GetLine`, `PyUnstable_InterpreterState_GetMainModule`, `PyUnstable_Long_CompactValue`, `PyUnstable_Long_IsCompact`, `PyUnstable_Object_ClearWeakRefsNoCallbacks`, `PyUnstable_Object_GC_NewWithExtraData`, `PyUnstable_PerfMapState_Fini`, `PyUnstable_PerfMapState_Init`, `PyUnstable_PerfTrampoline_CompileCode`, `PyUnstable_PerfTrampoline_SetPersistAfterFork`, `PyUnstable_Type_AssignVersionTag`, `PyUnstable_WritePerfMapEntry`
- **PyVectorcall** (3): `PyVectorcall_Call`, `PyVectorcall_Function`, `PyVectorcall_NARGS`
- **PyWeakref** (4): `PyWeakref_GetObject`, `PyWeakref_GetRef`, `PyWeakref_NewProxy`, `PyWeakref_NewRef`
- **PyWideStringList** (2): `PyWideStringList_Append`, `PyWideStringList_Insert`
- **PyWrapper** (1): `PyWrapper_New`
