What's new in the new version of lcc-win32
------------------------------------------
Oct 10:
  Fixed wrong offset calculation for anonymous structures within anonymous
  unions. Thanks to John for reporting this bug.
  Added functions remainder/remainderl/remainderf, trunc/truncl/truncf
  Updated the documentation of the math library.
  Added inline __declspec(naked) for defining inlined functions written
  entirely in assembly, and used this feature to implement the macros
  isgreater, isgreaterequal, and the other unordered floating point comparisons
  as mandated by C99.
  Fixed problem of spurious warning when using the %S format in printf.
  Fixed problem with offsetof macro.
Oct 6:
  Fixed problem when taking the address of atan function.
  Fixed the problem of powl returning a wrong result by mistakingly assuming
  overflow.
  Fixed problem with infinities in powq (qfloat) function.
  Fixed problem in the IDE when a cast to an immediate structure was used. The
  IDE assumed that no braces can occur when the level of parentheses is greater
  than zero. Wehn you use foo(&(struct m){"abc",10}); this rule is no longer
  valid. Since this feature was recently added to the compiler, the IDE issued
  a wrong message.
Oct 1:
  Fixed problems in xscanf and xprintf. Now xscanf can input qfloats, and long
  doubles.
  Changed all representations of complex numbers to a single representation
  unsing long doubles. This simplifies the code, the libraries and the
  compiler itself.
Sep 30: Fixed problem in strtrim. A wrong file was included in the makefile
  of libc.lib, what implied that a wrong libc.lib was built.
Sep 29:
  Added following functions to libc.lib:
  cabs, csqrt, cpow, cproj, csin, ctan, ccos, catan casin cacos, ctanh catanh,
  csinh, casinh, ccosh, cacosh, clog, y0, y1, j0, j1, yn, jn.
  All of them in qfloat/long double/double/ precision.
Sep 24:
  Complex numbers have been given a rework. Many automatic casts from float
  complex to long double complex for instance weren't working properly.
  The complex functions were moved from math.lib to the standard libc.lib
  to avoid having to link another extra library when using complex numbers.
  The cast of immediate structures (C99) now is working. You can write:
	(struct customer){"John",25}
  This is useful for passing immediate structures as arguments to functions,
  for instance.
  When generating complex long double constant like 2.3L+8.9Li the alignment
  of doubles in a 16 byte boundary wasn't respected, since the compiler failed
  to emit a 32 bit zero after emitting the first long double constant. Fixed.
  A faulty rule would provoke crashes when an addition to a base+offset form
  was being done.
  Complex math functions cabs, csin added with qfloat/long double/double
  versions.
  
Sep 16:
  Missing declaration for the constant INFINITY in math.h. Fixed.
Sep 12-14:
  The #pragma lib implementation had a serious bug. When linking several files
  that all contained an automatic reference to a library, the library would be
  added after that file was added. Since lcclnk is a one pass linker, the library
  would not be seen by the object files later on. For instance if we had:
    lcclnk a.obj b.obj
  If a.obj and b.obj included (automatically with #pragma lib) "foo.lib" the 
  command line the linker would see was:
    lcclnk a.obj foo.lib b.obj
  Now, if b.obj contained references to "foo.lib", that library contents would 
  not be seen by the linker what provoked incomprehesible errors... Fixed.
  Fixed overflow problem in powl function.
  Improved qfloat standard functions by reducing the copying of qfloat numbers,
  each 14*sizeof(int) bytes...
  Added functions ceilq ceill ceilf fdimq scalbnl scalbnf scalbnq log10q
  The floating constant 0q (zero qfloat) wasn't being recognized. Fixed.
  Added option -o <output file> to the compiler to keep it more compatible with
  gcc.
Sep 10:
  Added functions to the standard c library. cbrtl cbrt gamma lgamma erf expm1 
  atanl tanl isinl log1p and many other qfloat equivalents of the standard library.
  The pow function was tested for correct behavior with NANs and infinities
  The initialization -10q was not yielding a negative qfloat but a positive one.
  Fixed.
  Tables of qfloats now work as they should.
Sep 9:
  When an overloaded function like
  int overloaded fn(long double);
  was called with arguments:
  fn(5.9);
  The correct function was selected, but the call was done without generating
  the cast to the actual expected type!
  This means that many overloaded math functions failed when called with
  slightly different argument types. Fixed.
  The function hypot() was implemented in a trivial way. A better implementation
  was found in the code of free BSD. I adapted it, and added it to libc.lib.
  The power function was not taking into account the results specified for
  infinite, NAN or negative arguments. This has been fixed. Still, lcc-win32
  does not support negative zeroes.
Sep 6:
  Added coshl sinhl tanhl to the standard library. Added coshq sinhq and
  tanhq (qfloat versions) to qfloat.dll
Sep 5:
  Fixed problems with sinhl and asinhl in the math library. Those
  functions are part of the standard library, so they were moved from
  math.lib to libc.lib, and now will be included automatically in any 
  link process.
  Fixed problem with exp(2) being calculated incorrectly.
  Fixed missing WM_ definitions in win.h. Thanks Mike.
  Added windows system files rassapi.h adsiid.h and adtgen.h.
  The operators << and >> weren't being recognized. Fixed.
  The assembler was confusing fistpl and fistps..., thanks to John
  for pointing me to that error.
Aug 31:
 Added math library function log1p.
Aug 30:
  In an expresion (1) ? (double expression) : (long double expression)
  the resulting type would be long double and not double, even if the
  controlling expression was a positive constant. This made all those
  macros in math.h:
  if (sizeof(a) <=8) ? sqrtd(a) : sqrtl(a)
  FAIL, what a bug... This was visible only when you specified the -ansic
  flag, that is why I did not see it.
  Problem with qfloats and cast operators fixed. The expressions
	unsigned int a = (unsigned int) qfloat;
  would not work even if there was an operator
  int operator()(qfloat);
  defined. Besides, if the operator took a reference like in:
  int operator()(qfloat &);
  the reference was not being respected and this provoked a crash.
  Fixed trap when casting to long in some special conditions.
  Fixed several missing definitions in win.h
  Fixed the problem of redefinitions in win.h
  Fixed problem in oaidl.h: a macro was defined several times.
  Added warnings when there is an overflow when converting long double to
  long long and many other conversions that provoked an overflow without
  any warning message. This is in the context of constant expressions
  only.
Aug 28:
  Extra semicolon in math.h line 90 results in surious syntax errors.
  Fixed.
Aug 27:
  The format in printf "%lf" should ignore the 'l' qualifier. It wasn't, and
  mistook that for a long long (qualifer 'L'). Fixed.
  The standard accepts multiple type qualifiers in a declaration like:
  int const const const a;
  This wasn't recognized by the compiler. Fixed.
  The restricted keyword is now fully recognized and error checking is done.
  Added #pragma lib <bignums.lib> to bignums.h
  Added the corrections that Mike Caetano proposed for win.h. Thanks Mike.
Aug 26:
  Added intrinsics sin() and cos() to math.h. Now no function call will be
  generated for those functions but just a single fsin/fcos instruction.
  At the same time this greatly simplify the type generic math since now
  sin() and cos() will work for float to long double.
  Added VARIANT_TRUE/VARIANT_FALSE to win.h, that were missing.
  Fixed problem in xprintf when a long double value greater than DBL_MAX was
  passed: xprintf would print "inf" even if the number was a perfectly correct
  long double number.
  Improved constant folding in assignments. More constants are now followed.
  Fixed problem with rule CVUS(CVSU(reg)) in the win32.md file. This provoked
  that Comeau C++ would not compile its own library.
  Added improved register assignments. The overall speed is increased, I hope
  without too much pain in future problems. Please report any inconsistencies
  you may discover.
  Added simplification of (CON1*a) / (CON2*b) --> (CON1/CON2)*(a/b). The constant
  division will be made at compile time.
  Multiplies by 1.0 generate no code.
  When optimizing away a division by 2, a spurious move was still generated. 
  Fixed.
Aug 15:
  Added mount and umount utilities to the tutorial examples. Corrected win.h to 
  include the correct prototypes.
Aug 14:
  Updated inttypes.h to conform to the C99 standard. Several dozen macros were
  missing.
  Added __retry to the exception handling syntax. This construct can appear only
  within an except block and will provoke a jump to the start of the current
  try/except block
  
Aug 13:
  Finished the long long work for the time being. Several small problems with
  long long conversions and cnstant folding were fixed, and the tests passed
  again.
Aug 10:
  The cast (long long)&foo would not work. Fixed.
  When parsing 8LL << 6LL the compiler would not do the constant folding. Fixed.
Aug 8:
  Improved the 64 bit support. The improvement is like 25% or more, depending
  on the program. Please test anew your 64 bit applications.
  Rewrote wininet.h since to make it current with the SDK.
Aug 4:
  The function lldiv (and the corresponding lldiv_t) was missing from the
  standard library. Same with llabs. Both added.
Aug 3:
  __declspec(naked) was not working when the function returned a pointer. Fixed.
  Added intrinsic _GetStackPointer() that returns the current machine stack.
  Modified intrinsics.h, added the documentation, etc.
Aug 1:
  Added missing include file pdhmsg.h.
Jul 29:
  Fixed problem in compiler when two consecutive __try blocks were written.
Jul 25:
  Compiler: problem of expanding void inline functions fixed.
  Problem when expanding intrinsic function round to int fixed.
  Problem of memory overwrite when generating assembly listing fixed.
Jul 20:
  Due to a typing mistake in libcrt0.asm and libcrt0s.asm the version of July 19th
  produce corrupted DLLs. (!!!). Fixed.
  Implemented psignal(int sig,char *msg); that will print a signal with a descriptive
  text. Added a documentation entry for it, and the prototype in signal.h
Jul 19
  Implemented GetExceptionCode() and GetExceptionInformation() for structured exception
  handling.
  The result of __except() is now evaluated and must not be an integer constant. It
  can be, but this is no longer mandatory.
  Modified lcccrt0.asm to implement the global variables needed by GetExceptionCode()
  and GetExceptionInformation().
  Fixed problem in wresdll.dll: The second dialog box that was edited, the font would
  be of the wrong size.
  New header file <seh.h> containing all definitions needed for structured exception
  handling. Include this file if you want to use this feature.
  The alt left arrow (back in History) was not working correctly within wedit. Fixed.
Jul 17:
  Added the implementation of FLT_ROUNDS to float.h. 
  Added the intrinsic function _GetStatusWord() that returns the FPU status word.
  Changed the level of "Assignment in conditional expression" warning to a higher 
  warning level.
  When a default argument of type enum was used, the compiler trapped. Fixed.
  The automatic generation of help files from within Wedit has been discontinued.
  There are now better tools that are freely available like doxygen, for instance.
Jul 13:
  Fixed problem with offsets of unnamed structure/unions within a larger struct.
  Thanks to MrBCX for that.
  Fixed problem with sizeof variable length arrays:
  int fn(int n,int array[n]) { ... sizeof(array) }
  This produced a trap. Fixed.
  Fixed the problem with __try __except not working. This should run now but further
  work is planned.
July 9:
  Added the syntax ++operator for redefining the pre-increment operator. You redefine
  the post increment with operator++. This makes the operator overloading consistent
  with the normal C syntax.
  The operator sizeof wasn't giving the correct results when used with flexible
  arrays. As an example:
  int fn(int a) { int array[a]; ... int s = sizeof(a); }
  Added warning "Assignment within conditional expression", that catches errors like:
       if (a = b) { ... }
July 7:
  Fixed strrepl. Thanks John.
  The LPCREATESTRUCT was lost in win.h. Fixed.
July 6:
  Added roundl round and roundf functions from the C99 standard that were missing
  Added lrint lrintl lrintf llrint llrintl llrintf that were missing also. 
  The documentation (c-library.chm) was updated.
  Fixed bug of xprintf with negative numbers.
  Added the qfloat as a native type to the compiler. This allows a better handling
  of numbers like 123456889765645444566.414141414141444442222Q. Numbers ending with
  Q will be assumed qfloats. This is the first step of making the C99 feature of
  anonymous structures work.
July 4:
  Fixed pow function. When overflow happened, instead of returning infinity, 
  it was returning a NAN. Fixed.
  strtold() did not recognize the "inf" input. Fixed.
  The prototype of fegetexceptflag() was missing in fenv.h
July 3:
  Added all the missing definitions in windows.h proposed by Mike Caetano.
  Improved NAN handling and definitions in math.h
  fpclassify had problems, returning NAN for INF quantities. Fixed.
June 28:
  Added libraries npptools.lib mtxex.lib gpedit.lib fcachdll.lib rtutils.lib
  sensapi.lib credui.lib npptools.lib nmsupp.lib ntdsapi.lib sisbkup.lib
  winscard.lib
  Updated ntmsapi.lib odbc32.lib riched20.lib
  Added header files netmon.h gpedit.h filehc.h sensapi.h wincred.h sfc.h
  netsh.h ntdsapi.h schedule.h ntdsbmsg.h ntdsbcli.h winsmcrd.h scarderr.h
  winscard.h
  Updated winioctl.h
  If an inline function was defined but not used, the compiler would complain.
  Corrected.
June 22:
  The variable length arrays continue to give some problems. Some of them fixed
  specifically when using vla arrays in function declarations:
  int addmat(int x,int y,double matin[x][y])
  for example.
  Added the vertical property to progress controls. Up to now they could be only
  horizontal
June 17:
  The mergeasm utility is incorporated into the compiler now. The produced 
  assembler files will always have the C source text added to them. This
  eliminates the need of that utility.
  The printf j format was not recognized in the compiler and produced a 
  spurious warning. Fixed.
  The qfloat.h had a line #pragma lib "qfloat.lib". This produced that the
  library would be searched in the current directory instead of in the 
  standard include files. Fixed. Now is #pragma lib <qfloat.lib>
June 16: Added traffic.h to go with traffic.lib (import lib for traffic.dll)
  Added oleacc.lib cryptui.lib cryptnet.lib cryptui.lib odbcbcp.lib netsh.exp
  Added icm.lib ,sfc.lib and sfc.h, wsnmp32.lib
  Did you notice that this file has less typos?
  John Finlay has corrected them. I thank John for this and all the work he 
  has done with the lcc-win32 compiler
  The memory dump in the debugger did not display correctly. Fixed
  The "Running" button in the debugger stayed on ofter the debugger closed.
  Fixed.
June 15:
  Added warning with level 3 to the compiler: assignment of unsigned char * to 
  char *.
  Cleaned win.h eliminating declarations like
   DWORD bitfield:1
  and replaced that with
   unsigned bitfield:1
  This reduces the number of warnings in win.h when using the -A option
June 12
  Added import library wldap32.lib and corresponding header files
  winldap.h schannel.h
  Fixed problem in wedit about the Open project dialog that did not correctly
  sort the projects by access date.
  Added support for owner draw listboxes in resource editor.
June 1:
 Fixed several problems in the resource editor: the "name" field was inconsistently handled when
 the properties window was open. Fixed. Other problems with symbols were fixed too.
 Wedit: The "name" field when opening a new file contained garbage at the start. Fixed.
 
May 23:
 Some problems in Wedit fixed: undo feature (yes, still that), the file comment
 utility was not working, and some others.
 The tutorial was ported to PDF format, and many chapters were enhanced and
 corrected. More material was added explaining finer points of C, and several
 examples were added to the C samples at the end.
May 16:
  Fixed problem when taking the mod of 2 characters using 64 bit arithmetic.
  Fixed problem in reference arguments and constant folding
  Fixed problems of wres with spaces in file names.
May 11:
  Added the statement MANIFEST to the resource compiler. This compiles a resource for the
  manifest of windows NT. Syntax:
  1 MANIFEST "file name here"
  The compiler would not accept:
  qfloat a = 6.6e834Q;
  Fixed.
May 2:
  Added a warning when a single line comment was finished by a backslash
  Added several rules to optimize expressions where character  arithmetic is performed
April 27
  When you define a structure within a special packing environment, the definition of all
  structures of that type should take into account the packing when the definition of the
  structure was done and NOT the packing in effect when the structure is initialized. For
  instance:
  #pragma pack(push,1)
  typedef struct tagStr { char a;char b;} Str;
  #pragma pack(pop)
  Str someVar = {1,1}; // Here we have a normal packing but the structure was defined with
                       // packing to 1. THAT should be used here.
April 26
  Problem when generating code for a modulo expression when using long longs. Fixed.
April 23
  Fixed problem in the spiller. When several character comparisons were done in a single
  expression (i.e. charray[2] == chararray[34] && chararray[34] == chararray[12] && ... )
  the compiler ran out of registers. Fixed.
  Fixed problem with importing cabs (complex absolute). The crtdll.exp file was wrong.
  Improved debug information for variable length arrays.
  Improved code generation when pushing a structure by value in the stack, or copying it into
  another. If the size of the structure is a multiple of 4, I will use movsl (4 bytes at a time)
  instead of movsb (1 byte at a time).
April 19:
  Added header files and export descriptions for Bluetooth support using windows sockets. Files
  irprops.exp ws2bth.h and others.
  The compiler would not recognize L "some string" as a wide char string. Fixed.
  Winhttp interface was added to the system. A sample for this was added to the lcc2 distribution.
  The implementation of the variable length array had to be rewritten. The information about this
  type of array was stored in the type information, but this led to assuming unrelated objects
  that had the same type were variable length arrays. Now the information is stored in the symbol
  itself.
April 6:
  Updated directx 8 according to John's contributed files.
  First version with the strlib (a clone of the standard string library with bounds checking)
  Written by Friedrich Dominicus.
  
Mar 31:
  When an inlined function was compiled using the O2 flag (eliminate unnecessary assignments), a
  NECESSARY assignment was erased. Fixed.
  Still problems with the pow function when passing floats. Fixed.
  The documentation for all the windows controls has been added to the lcc documentation.
Mar 23:
  Fixed problem in __ftoul: the register ecx was being thrashed
  Fixed bug in optimizer when compiling (expression)?1:0
  Fixed compiler trap when compiling an array of undefined structures. The compiler will now emit
  a diagnostic message...
  Fixed bug of wedit: when the debugger stops, the current directory was changed to <project>\lcc
  instead of the current directory when it started the debugger.
  
Mar 10:
  Fixed problem with token pasting in the preprocessor.
  Added 0b11100 for binary constants
  Fixed problem with strrepl when statically linking
Mar 2
  Resource editor properties window is now non-modal, i.e. you can change the
  focus without closing it. This could produce some problems. Please report as needed.
  buildapi.exe was not working. Fixed.
  Debugger: The expression a[b[c]] was not displayed correctly. Fixed.
  Various problems with the resource editor were fixed.
  Imaginary numbers can be written 78.98i or 78.98I
Feb 14:
  The profiler display was not working. Fixed.
Feb 9:
  Added strrepl (replace a substring with another string) to the standard library.
  Corrected problem in browsegen.exe that produced inaccurate debug info. Please erase your
  old xrf files
  Corrected problems with temporary results in expressions that use overloaded operators.
Feb 8:
  Added dbglib.exp and dbglib.h to the distribution.
Feb 6:
  Added functions ctanh casin log1pl casinh. Fixed problem in csin, ccos and clog. All functions
  come in 3 flavors: suffix l for long doubles, no suffix for doubles, and f suffix for floats.
  The compiler would not initialize a long double _Complex because of alignment problems. Fixed.
Feb 5:
  New executable profiler.exe will display the data of the profiler. The usage is: 
  profiler <data file>
  where <data file> is normally the __profile.data produced by a program compiled with profiling
  support. This will be done automatically by the ide.
  The problem of the resource editor not saving header files is fixed.
  The compiler supports now compiling for dynamically linking with the lcc runtime (lcclibc.dll)
  that is now shipped with the distribution.
  A problem with the addition of 64 bit integers was fixed.
  wedit now will take into account the left margin when drawing the red underline when a symbol
  is undefined.
  New compiler flags:
   -libcdll Compile for linking with lcclibc.dll
   New linker flags:
  -dynamic Use lcclibc.dll instead of libc.lib (static version)
  -nolibc No linking with the c runtime at all
  Problem with buildapi crashing at installation fixed.
Jan 24:
  Profiler support improved.
Jan 21:
  Fixed (hopefully) the nasty crashing in wedit. Fixed another bug in the compiler when calling
  a _stdcall function that returns an unsigned short. Thanks to "lcc-user32" for his/her detailed
  bug report.
Jan 19:
  Cleanup of wedit's code to avoid nasty crashes when checking the syntax. I hope it works better
  now.
  The system was wasting several handles when starting a new process. Fixed. This would not make
  a lot of difference, but now that the editor can span several threads in a minute it could become
  significant.
  The expression 1%1 was NOT giving zero as it should. Fixed.
  The coverage profile was started, the user interface modified, but the data display part is not
  ready yet.
Jan 14
  Fixed problem in the compiler when converting doubles to unsigned ints. For some values the conversion
  was wrong.
Jan 8
  Added functions acosl acosf hypotf hypotl to libc.lib
  Fixed font problem in the new resource editor
Jan 3 
  Added two new intrinsic: lrotl and lrotr, to rotate an integer a number of bits.prototypes in
  stdlib.h
Jan 1 2003
  The new version of the resource editor is out. The documentation has been totally rewritten since
  it is a new user interface.
  The entire documentation of the system is now in chm form. It is no longer distributed with the
  main distribution but in a separate file, where there is the tutorial, the users manual, and
  the documentation for the standard library.
Dec 28:
  Michael Scott discovered a problem in the routine _alludiv, that was giving a result 1 less than
  what it should. (That routine divides long longs). Fixed.
  Several bugs in the undo feature of wedit were fixed.
Dec 27:
  The resource compiler has the -M and -Fo options as the compiler has. This will allow wedit to
  find the dependencies of a resource file, and recompile the resource file when one of the
  contributing files has been changed, for instance an icon or similar. This wasn't working till
  now.
  The "Open project" dialog has been updated to show the details of each project as the user
  changes its selection.
  Two problems in the register allocator were fixed today; I thank grischka for very helpful
  bug reports.
  Makefile generation, has been improved (see above) and now the resources are relative to
  $(SRCDIR) instead of being absolute paths.
  The position of the child windows is now correctly restored. The first time you load the new
  wedit the windows will be wrongly positioned, since the coordinates now are relative to the
  invisible MDI client window. Do a "cascade" to reposition the windows, then the position will be
  correctly remembered.
  The zlib library has been added to the standard distribution. zlib.dll is in \lcc\bin, zlib.h
  is in \lcc\include, and zlib.lib will be built at installation in \lcc\lib like all the other
  import libraries.
Dec 24:
  New functionality "Check syntax" added to the IDE.  This feature does:
  1) Copies the (possibly modified) contents of the current editor file (that must be a .c file) 
    into a temporary file in the binaries directory. The name of the temp file is __check_%s where 
    after the prefix the name of the original file is appended. The editor buffer is not saved.
  2) Calls the compiler with the current compile options of the project and adds the option -check 
     to the compiler flags.
  3) Under this new option the compiler will not produce any output, just diagnostics, if any. No 
     object file is created. Since all code generation is disabled, such a test runs approx. twice 
     as fast as a normal build. This option increases the warning level to -A too, so you may find 
     more diagnostics than you otherwise obtain when compiling with your compiler options.
  4) Diagnostic output is saved in the project error file (normally in the binaries directory). 
     The temporary c file is deleted.
  5) The "build" window opens at the bottom of the editor and shows the diagnostics, if any.
  You press Alt+F9 when you want to check your code.
Dec 20:
  exp2 function added
  log2 function added
  expm1 function added in assembly to libc.lib and taken out of math.lib (was in C)
  imaxabs function added
  fixed inttypes.h to declare all C99 types
Dec 18:
  The header file olectl.h was wrong. Updated.
  The compiler would not recognize correctly a number like: 04000000000LL, i.e. an octal long long.
  Fixed.
  xprintf would not print correctly a negative octal number, that should be treated as unsigned.
  Fixed.
  xprintf did not recognize the qualifier 'L' for octal numbers. Fixed.
  xprintf would not print correctly a nonsense specifications like "%.10000f". Fixed. Output is
  truncated at 4095 chars, according to the standard.
  Wedit did not correctly recognize an input file name. Fixed.
Dec 17:
  Added nextafterl nextafter and nextafterf library functions
  Added mshtmlc.h mshtml.h dimm.h to header files
  Fixed problems with the fsopen function
Dec 13:
  Serious problem in the debugger evaluator fixed.
Dec 12:
  Problem with conversion of INT32_MIN to long long fixed.
  Added several complex math functions in all their versions (float double or long double _Complex)
  Complex.lib has been eliminated and integrated in math.lib. This simplifies the user interface:
  only one lib to remember.
  Problem with the Undo fixed (I hope is the last one)
Dec 3:
  Problem with the #pragma lib instruction fixed.
  New version of Wedit supports tooltips for all functions in the header files (windows and
  standard headers). This supposes a tooltip database that increases the distribution size by
  more than 200K. I think this is a small price to pay for this useful feature.
  A serious problem with the GC and the Undo information was found. I hope now the problem is
  corrected: I was storing the Undo pointer at an odd address.
Dec 2:
  The editor would not recognize errors/warnings that contained two (or more) file names when
  reporting an error in an include file, for instance. Fixed.
  Fred Tydeman reported several bugs with the overloaded exp function. Fixed.
Dec 1:
  A complete rewrite of the "Undo" feature was done. There are still bugs in it but I hope now
  it will work better. This needed several days of work.
  The compiler emitted a warning when a static variable was assigned but not used. This is
  incorrect. Fixed.
  Problems still with 64 bit integer comparisons fixed.
  Stdint.h defined some quantities incorrectly. Fixed.
  A cleanup of the editor code was undertaken. This reduced the size of Wedit by 6K.
Nov 28:
  Editor:
  Corrected problems with indent block-move. The line right after the block move was being moved
  without reason. 
Nov 26
  Added mmc.h
  Fixed problem with comparison between long longs and chars
  Fixed errors in stdint.h
  A number like -388766LL would lead to generation of a run time NEGate with 388766 as argument.
  Fixed. This is jow done at compile time.
Nov 23:
  The linker left an invalid executable in some situations. Fixed.
Nov 19:
  Optimization problem discovered by the SmallEiffel team fixed.
  Arranged setupapi.h. A total rewrite was necessary.
Nov 16:
  Problem with a wrong instruction being issued fixed. Thanks to Uwe Sander.
  Added winsnmp.exp (snmp/windows API)
Nov 9:
  Fixed problems in shlguid.h. Thanks to Pascal Haible for reporting me this.
  Added olepro32.lib (olepro32.exp)
  Added the GUIDs for Oledb.lib
  Updated pdh.lib
  Added pwrprof.exp powrprof.h (power management), toolbar.exp (toolbar creation),
        uxtheme.lib uxtheme.exp uxtheme.h
  Updated psapi.exp, resutils.exp.
  Lcclib was stopping after 700 files when you gave it an argument like "*.obj". Fixed.
Nov 7:
  First version of the embedded web control shipped. Use iehelper.h and link with iehelper.lib.
  The library uuid.lib will not be shipped with the distribution but can be built with the
  command buildguid.exe. This saves 3.5MB in the distribution, that already is too big.
  This command uses a IIDs template in \lcc\buildlib\uuids
  Added addl.h to the standard includes, and adsguid.lib (directory service) import lib.
  Added bits.h (background downloading header) and exdisp (web browser interface) to the
  header files
  Added authz.lib: for the authz.dll, authorization API.
  Added authz.h: ditto for the definitions, together with adtgen.h (auditing)
  Added dsuiext.lib: Directory Services User Interface Extensions
  Added httpapi.lib. (Httpapi.dll import lib)
Nov 1:
  Fixed bug in strtold. Thanks to Vicent for pointing me to this bug.
Oct 29:
  The double variable HUGE_VAL (defined in math.h) provoked a trap when used. Fixed and thanks to
  Ricardo Y Maeda for reporting this bug.
Oct 28:
  The wedit.hlp file has been replaced by c-library.chm, a compiled html file. Thanks to John for
  doing the conversion.
  The debugger was being fooled when you have a typedef in some other file that had the same name
  as a local variable or argument in a function where this typedef wasn't used. Fixed.
Oct 25:
  The debugger should be able now to follow programs with several threads.
  Bug in code generation fixed: When taking the modulo of a number by two (n%2) the compiler
  generated an incorrect instruction.
Oct 21:
  Problem with xprintf substitution fixed. Thanks to Geoff from elj-win32.
Oct 18:
  In the declaration:
  void fn(void (_stdcall pfn)(int)) { }
  the pfn parameter (a function pointer to a _stdcall function) was NOT recognized as _stdcall.
  This is fixed. Thanks to grischka for pointing me to this.
  Besides, in the declaration
  void fn(void _stdcall (*pfn)(int)) { }
  (the _stdcall was before the parentheses instead of after it) the _stdcall was ignored. Fixed.
  In the IDE, when you defined the output of the executable to be somewhere else than in the directory
  where lcc keeps the object files and executables, a boring message "executable not up-to-date" was
  issued each time the debugger was started. Fixed. I added a variable EXE in the makefile. Please
  watch for problems here.
  
Oct 16:
  File poppack.h was missing in the distribution. Added.
  The debugger did not handle correctly modules with multiple source files, i.e. when in file
  file1.c you wrote #incldue "file2.c". I hope this works better now.
  Code generation error with long longs fixed.
  
Oct 11:
  The printf checking for floating point formats was not working. Fixed
  Code generation error when pushing a character argument to an overloaded function fixed.
  A typo in libc.lib made fpclassf (float fpclassify) invisible since it was exported under a
  wrong name.
  A small inaccuracy in LDBL_EPSILON in float.h was fixed.
  The header files for the shell (shlobj.h shobjidl.h and others) were updated to reflect the
  latest development as used for the Microsoft Windows SDK version Oct 2002. This should be
  transparent to users unless I have make a mistake obviously.
  The import libraries shlwapi and shell32 were regenerated to reflect changes in the corresponding
  DLLs.
Oct 2:
  The function exp() of the math library was wrong. Fixed.
Sep 26:
  New version of the compiler that has a 30% improvement when compiling very large files. (4MB)
  Modified the "window" menu item in the IDE. Now, you will find there the most recently accessed
  files. You can configure the number of files in the configuration->workspace tab. A value of
  zero means no limit. The "More windows..." menu item leads now to a bigger dialog box than the
  default windows MDI one.
  The "import project" option had several problems. Fixed.
Sep 18:
  Glaux.lib updated by John with a correct version.
Sep 13:
  dynloader.h was wrong. Fixed
  Arranged the "window" menu of wedit. Now you can see all the windows opened, or set a maximum
  number of windows to be kept in the windows menu.
Sep 11:
  When an assignment to a structure smaller than 4 bytes is done, the compiler optimizes this
  as if it were an assignment to a register. This was generating wrong code when you had:
  struct foo a,b,c;
  a = b = c;
  The first assignment was not transformed, only the second was. This provoked a compiler failure.
  Fixed
Sep 10:
  Several problems were discovered with optimizations: constant folding was provoking a trap in
  some special code patterns, and some constant foldings were not reset after a while or do
  construct.
Sep 7:
  The debugger did not always show the current line. Fixed.
Sep 5:
  lc.exe (the compiler driver) did not handle .asm files correctly. The same problem appeared with
  .def files. Fixed. Besides, the compilation of a single .res or a .asm file will not invoke the
  linker.
  The make utility was modified to set always the variable LCCROOT. This allows that wedit generates
  makefiles without absolute paths in it. This variable will be set to the lcc directory.

  Wedit has now an incremental search feature, just like emacs. It is bound to Ctrl+F, and allows
  you to type a word to search, incrementing the position of the cursor with the longest match.

  I have started to build the data structure browser in the debugger. Not finished yet.
  
Sep 1:
  Added incremental search to wedit.
Aug 25:
  The compiler would emit wrong code when a bit field that has the same bit size as the natural
  size of the bit field type appeared in a conditional expression. For instance:
    unsigned int f:32; // 32 is the bit size of an int
   if (struct.f)
   In this context (and only in this setup) the if would be incorrectly generated. Fixed, with some
   pain. this was a difficult bug to cope with.
   Thanks to John for pointing me to this.
Aug 24:
  The construct
	int a[4.9]
  was not handled correctly by the compiler. Fixed, now there is a clear error message.
  The optimizer was spoling the generated code when you had:
	char a[]= {'a','b','c'};
        a[1]--;
   Fixed.
Aug 22:
  The linker contained a bug when exports were added to an executable that wasn't a dll. Fixed.
  The prototypes for nan(), nanf() nanl() were missing, as well as the implementation of those
  functions. Added.
  The rules for generic functions have been slightly modified: if there isn't any exact match, 
  before applying all the promotions, only the promotions as to widen the argument types are
  applied: char/short are converted to int, unsigned char and unsigned short are converted to
  unsigned, and floats are converted to doubles.
Aug 2:
  The problem of executables containing random bits after  the import table has been fixed.
  The linker version has been upgraded to 2.55 since some APIs require this.
Jul 23:
  When comparing long long integers right after a not operation, the compiler could generate wrong
  code. Fixed.
Jul 20:
  Added several include files and libraries to support WMI network API: wbemads.h wbemdisp.h
  wbemidl.h wbemcli.h wbemprov.h wbemtran.h. The new library wbemuuid.lib was added to the
  distribution.
  The compiler distinguished between unsigned long and unsigned int when comparing types. Since
  this types are always equivalent in lcc-win32, any error generated by "redefinition of" was
  wrong. Fixed. This means that void function(unsigned long) is equivalent prototype for
  void function(unsigned);

Jul 15:
  Unsigned/signed comparisons in the preprocessor were wrong. Fixed.
Jul 9:
  The compiler would generate wrong code in some situations for the expression:
  int Table[idx & CONSTANT] += short;
  This error was introduced around May 1998, and was present since... It only appears
  in special situations of register configuration, and that's why it was so
  dificult to detect.
  Math.h was modified to comply with ANSIC 99 generic macros. The standard library
  was enhanced too.
Jul 1:
  Several problems in the debugger fixed. Pointers to simple data types were sometimes incorrectly
  followed. The memory window did not know how to display qfloats correctly.
  Qfloat support improved. The new qfloat library features more speed, less size.
Jun 28:
  First release of the #pragma lib construct. Syntax:
  #pragma lib "path"
  #pragma lib <path> (look in \lcc\lib)
Jun 25:
 Serious bug in math.h discovered by Fred Tydeman:
  The prototype for rint is wrong! In math.h I changed int rint(double) to
  the correct double rint(double)!!!
Jun 23:
  Following functions were added to the math library (now named math.lib):
  acoshl asinhl expl ldexpl frexpl ceill atanl expm1l
Jun 22:
  Browsegen generated an extra file name in the file table of the browse information.
  This made wedit open the wrong file when going to the definition of a symbol. Fixed.
Jun 21:
  Fixed problem with inlined functions that generated several labels of the same name.
  Fixed another problem with inlined functions when a label wasn't generated at all.
  Fixed strtold, and added it to the compiler when parsing long doubles.
  Added correct run time library functions for converting from double or long double
  to short or character.
  Fixed declaration problem for rint in math.h
Jun 20:
  Added explanations for the compiler and linker configuration to the documentation
  right after the "Installing lcc" item. This will help new users to quickly find
  out how to use the system.
  Sometimes the usage count of a function used in a call wasn't incremented and 
  its symbol usage remained zero. This leads to the assembler not emitting an
  .external record to inform the linker that this function was extern and used.
  The linker would then complain with an incomprehensible message "undefined
  symbol (*UND*)". I ensure now that this reference counter is incremented whenever
  I emit a function call.
  The system dll MSIMG32.DLL was missing from the system. Added. Thanks to
  manuel.francisco@free.fr 
Jun 19:
 Fixed spurious complaints in the compiler when a long double was given to %Ld.
 xprintf was writing the wrong quantity for %ld when the input was
 negative. Fixed.
Jun 17:
  This version has the qfloat library integrated within the compiler to
  increase reading precision. Xprintf has been updated to use long
  double arguments and in general precision of reading and writing of
  long doubles is now higher.
  Problem when accessing long long array fixed. Bug reported by Hubert S.
Jun 11:
  Fixed problem with sizeof in structures with flexible array members.
  Started working in a better memory display window for the debugger.
  Added long double logl(long double); 
        long double log10l(long double);
        float log10f(float);
        float logf(float);
	int fpclassl(long double) int fpclassf(float) int fpclassd(double)
   to libc.lib. All written in assembler for speed.
   The corresponding definitions were added to math.h. This functions are
   defined as overloaded functions.
Jun 9:
  The assembler instruction andps was missing. Added.
  When generating code for a constant double or float, the compiler would not
  generate code for zero or 1, generating inline instruction fldz and fld1.
  When a NAN or an INF number was generated as a result of a double/float
  constant expression like 1.0e99999f, this tests would fail and the compiler wrongly 
  assumed that a NAN or an INF was zero, producing a wrong code, and the dreaded
  "compiler error in kids". This is fixed now. I test for NANs when testing for zero
  double constants in dag.c
Jun 9:
  The assembler instruction andps was missing. Added.
Jun 5:
  Working together with Fred Tydeman, I have corrected several problems
  with conversions from floating point types and integers/unsigned. The
  conversions were done in a correct form, but the flags weren't at all
  like the ANSI C standard specifies. In several cases the INVALID exception
  wasn't raised even it it should, and in other cases the exception was
  raised even when it shouldn't. Missing still in this area are the conversions
  from/to long doubles/doubles to signed/unsigned chars.
Jun 4
  Added a correct library function for converting a long double into an
  unsigned long long. Since this operation is not implemented in the FPU
  it has to be simulated in software.
  A variant of the long long division had the divisor/dividend inversed. Fixed.
Jun 3
  Problem with overloaded keyword fixed.
Jun 1
  Bug in the optimization of expressions like !v fixed. This bug appeared
  only when at the left hand side of the assignment there was an indexed
  table of structures and at the right hand side a boolean expression.
  Added mstcpip.h to the distribution.
  Fixed a bit the Undo feature of the IDE.
  
May 26: Bug fixed when optimizing a memset call by generating it inline.
  Thanks to Mario Feldberg.
May 25:
  The debugger wasn't testing for NANs and printed 0.0 instead. Fixed.
  The __declspec(naked) was added. This construct is now 100% msvc 
  compatible: no "ret" instruction generated, no prolog, no function
  epilogue, exactly like msvc does. This means that in 99% of the
  cases it will cause your program to crash. BUT you can write device
  drivers with it.
  xprintf has now the modifier ' (single quote) that will allow you to
  put the thousands separator in the formats d and f or g. Example:
  xprintf("%'d\n",123456789);
  will print:
  123,456,789
  Fixed problem with "%*.*s" in the printf checker.
May 20:
  Added qfloat support in the debugger.
  Corrected a small problem in richedit.h header file.
  Added vdmdbg.h windows header file.
May 16:
  When a structure that had a size less than 4 was declared as a
  local variable and optimizations were ON, the generated code would 
  write more bytes than necessary to the stack. Fixed.
  When a field assignment was done, the optimizer did not consider
  if it was a structure field. For instance x.field = 23 would be
  seen as x = 23. This confused completely the constant folding
  module. Fixed.
  New module added to check for the arguments of printf, fprintf, 
  and other functions of the same family.
  When an overloaded function that expected a reference arg was
  called with a structure argument, the compiler failed to take
  the address of the structure and pushed the whole structure
  instead. Fixed.
  When an overloaded function was called with a structure argument,
  the compiler failed to recognize this and issued a "No matching
  call" diagnostic. Fixed.
  The documentation for the qfloat data type was added to the html
  manuals. Now there are two versions of that library: a dll version
  with the minimal functionality and a static version with extended
  functions.
May 14:
  The statement:
    static char *foo = ("abc");
  wasn't correctly handled by the compiler. Fixed. Bug reported by
  Mario Feldberg.
May 11:
  MANY bug fixes contributed by Fred J Tydeman. They are mainly in:
  boundary conditions and constants initializations when compile
  time operations are involved. For instance:
  23.778L/123.9L, or 1332LL * 23322LL, and similar operations. I 
  have corrected many of them, specially in conversions from/to
  long doubles and long longs.
  Code generation bugs were discovered by Mr. Tydeman. Comparisons
  of greater than or less than involving unsigned longs and doubles
  were generated incorrectly.
  Mr Tydeman looked in the xprintf implementation too, and found many
  inconsistencies:
  xprintf with DBL_MAX was printing a wrong value. Fixed, I increased
  the digits buffer to accommodate at least 350 numbers.
  The strtold function would return a wrong value for 0.0e9999. Fixed.
  There was a problem with the constant folding module that would lead
  at the end of a switch statement (and if there wasn't any break at
  the end) to propagating a constant wrongly outside the switch. For *
  instance:
   switch (b) {
	case 1: c=12; break;
	case 2: c=34;
  }
  // here the compiler assumes wrongly that c is 34. Fixed.
  In the module that creates a multiplication by the inverse of a 32
  bit constant there was an error that would surface only in very
  complicated contexts. Thanks to the SmallEiffel compiler I found
  that one.
  A new module for extra precision floating point is now part of the
  standard distribution. This floats have 104 digit precision. The
  dll involved is qfloat.dll, that now is part of the distribution as
  well as the qfloat.h file.
April 30:
  When an overloaded function had a float argument, the compiler
  was pushing a double instead of a float! Fixed.
  Some redefinition errors appeared after the corrections to win.h.
  Fixed.
  New command line option to wedit:
	/projectsroot:<file path>
  allows you to designate an alternative path for the projects
  directory.
April 28:
  A lot of work being done for the bignums. A general cleanup of
  the code yielded missing/buggy operators, and some serious defects
  in the assembler code for the bignums that was rewritten.
  The first implementation of a "constructor" feature. If you define
  a typedef, now you can define a function with the same name that
  will be called to build the object.
  For instance:
  typedef struct example { int i; } EXAMPLE;
  EXAMPLE EXAMPLE(int a) 
  { 
      EXAMPLE result;
      result.i = a;
      return result;
  }
  Then you can write:
  EXAMPLE g = EXAMPLE(56);
  This would be trivial, but I plan to call this functions whenever
  a cast is necessary and this functions exist.
  
April 27:
  Improvements to win.h: raw input API incorporated. Other missing
  definitions added.
  The #error directive didn't increase the error count! Fixed.
April 19:
  Optimizations improvements reduce code size: Instead of 
       movl 	$0,destination, I emit now
       andl	$0,destination
  what is 4 bytes shorter as instruction. This makes for big code 
  size savings.
  When you pass an immediate constant to a function that is 
  expecting an argument as reference, the compiler issued an error.
  This is corrected now. The compiler will build a temporary that
  contains the constant, and pass the address of this temporary 
  to the function as C++ does.
  Several new trees were added to the rules of win32.md.
  The bignums accept now the assignment operator, i.e. you can
  write: pBignum a = 23; This will call automatically the operator
  assignment for this type (bignums). This wasn't working before.
  Note that you have to define the assignment operator function as
  a function that accepts a reference for it to work at all.
April 8:
  Several problems in the bignums.dll fixed.
  Some minor problems in wedit fixed.
March 31:
   Fixed spurious warning "variable used before being defined", when
   a sizeof() of an uninitialized variable was done
March 29:
  Bug fixing update. Several problems with long longs, and ternary 
  expressions.
March 22:
  Two bugs in optimized code generation fixed:
  a) When the following code was compiled, the result was incorrect:
	char *string = "Oops";
	printf ("%d\n", (*string == '\0')? 0 : -1);
     This was a problem of the boolean assignment generator, that 
     did not complement the carry when the arguments were exactly
     zero and minus 1. Bug reported by Konrad Budych from Poland.
   b) The following code was incorrectly implemented:
	int test() {
	char c[] = {0,0};
	return 0;
	}
      This provoked a stack corruption. Bug reported by Tom from Japan


March 15:
  The constant folding module has been completely rewritten and 
  tested. This supersedes the old implementation, too far back in
  the back end, that was incomplete and bug-ridden.
  The module for emitting the debug line number information corrected
  an error with more lines being emitted for the same code.
  Several fixes to the header files, updates, etc.  
Jan 25:
  The compiler will now check for operators with a void return
  type and will issue an error if this is found.
Jan 23:
  Major update.
  o Speed of 64 bit operations improves from 70.000 bits/sec in 
    the "tiger" benchmark to 200.000 bits/sec. lcc-win32 achieves
    now  79% of the performance of fully optimized gcc.
  o Improvements in analysis yield a better CSE (Common Sub-
    expression Elimination) that reduces the number of redundant
    loads from RAM into a register. This improves the speed of 
    the generated code.
  o Several bugs were fixed in the 64 bit (long long) module, and
    in several other places: the pushing of a structure of 4 bytes
    is now done in a single instruction instead of in a block
    copy operation. This fixes a bug where register esi was lost.
    
Dec 25:
  Problem in the optimizer solved (reported by Ross Axe).
  Wedit "Evaluate expression" should be able to handle simple
  casts now.
Dec 13:
  Error in operator overloading corrected. The arguments were not 
  promoted to their expected types.
  Complex.lib was modified to expect now _Complex types instead of
  references to _Complex.
Dec 10:
  Error when a stdcall function pointer was being accessed through
  a function pointer. This bug appears only when the stdcall function
  returned a boolean result.
  Update to the documentation (wedit.hlp)
  Now xprintf will have enough precision to display accurately
  1e18+1. Completely rewritten.
  Several functions (floating point) added to the standard library.
  modfl, for instance.
  The driver program lc.exe was updated. Some minor glitches when
  using paths were fixed.
  The new startup code sets the precision settings to 64 bit
  precision (full precision)
Dec 8:
  Error in generated code when the try ... except construct was used
  corrected.
  Functions added to the standard library: (for C99)
  fegetround, fesetround, DoublePrecision, FloatPrecision, FullPrecision,
  isnan, floorl, fmodl, fmaxl
Dec 2:
  When a shift right was immediately followed by a shift left, 
  in some cases the register allocator would emit wrong register
  assignments. Fixed.
Dec 1:
  Problem in the optimizer fixed: the register ECX is not actually
  free if there is a shift operation further down the same block.
  When an inlined procedure with no result is expanded, problems
  occur. Fixed, but still some problems remain in there.
  The new functions xprintf, etc, did not test for NANs and INFs,
  and they would loop infinitely if given such "numbers". Fixed.
  Wedit had a fixed size "Add project files" window. When the
  paths were too long, you could not see all the file name. Fixed,
  now that window is resizeable.
Nov 29:
  The lc driver program for lcc would not add the libraries given
  in his command line to the linker. Fixed.
  The lc driver program would not handle correctly paths. Fixed.
  The compiler now supports the correct syntax for
	double _Complex a;
  The structure double _Complex, long double _Complex and float
  _Complex are built-in data-types as specified by C99.
   libc.lib was missing ldtoa. Fixed.
Nov 28:
  Fixed bug when doing integer division.
  Wrote ldtoa to handle displaying a long double. Needed for the
  debugger.
Nov 25:
  Added fdim nearbyint fmax and other functions to the standard c 
  library.
  Added libml.lib to the standard distribution. This library contains
  all long double functions, and many other goodies. It has been
  adapted for lcc-win32 from the cephes math library available in
   www.netlib.org. Lcc-win32 compiles now all this long double code
   without any error.
  When you declared a flexible array, and immediately afterwards another
  (normal) array, the second array would be treated as flexible and the
  compiler would crash. (What a shame!). FIXED.
  When the bignum library got an overflow, it showed:
	Error 2116
  Now it shows:
	Overflow 
  An improvement I hope (:-)
Nov 23:
  When a function is being overloaded, the compiler should correctly
  promote the void pointer to match any pointer. This wasn't being done.
  When several overloaded functions were declared, the compiler did
  not emit all the names as external symbols to the assembler, and
  link would fail. Thanks to John Findlay for reporting the two
  bugs above.
  When an unused variable in an inner scope was declared "register",
  the compiler would allocate a register for it, spending a register
  for nothing.
  When long long operations are being done, it is impossible (at
  least for now) to use any register variables. The long long operations
  use too many registers.
  Several long long operations were wrongly described in the machine
  description.
  The optimizer is improved, generating tighter code. Instead of:
	movl	%esi,%edx
	addl	$8,%edx
	orl	$4,(%edx)
  it will generate now
	orl	$4,8(%esi)
  Instead of generating an division to compute the modulous of an
  integer by a power of two, the compiler will generate several 
  and or OR operations.

  long doubles are now implemented as a real type, i.e. they will
  not only be recognized but also actually used. This has provoked
  obviously quite a lot of modifications.
  The function "peektoken" would not work when the compiler was almost 
  at the end of the buffer received from the preprocessor. Fixed.
  Wedit has been updated to display long doubles in the debugger.
  The compiler emits now that information of course.
Nov 12:
  Fixed bug that produced a division by zero. Thanks to Anh Vu Tran for his 
  report.
Nov 11:
  Nasty repaint bug in wedit fixed. Diff dirs improved, and other small
  changes to wedit.
  Added fix for _GetCmdLine proposed by kdiggins@qwest.net. lcccrt0.asm
  and lcccrt0s.asm modified.
  Fixed problems in the examples distribution.
  The first file in the files open toolbar did not work. Fixed.
Nov 7:
   Fixes problem in code generation. Under optimization, a switch 
   that used a signed byte variable would fail when comparing negative
   bytes.
   The disassembler now understands all pentium 4 operations.
   Inverted Mousewheel support in Wedit due to popular demand
Nov 4:
   Several problems in the long long code generation fixed, and the overall
   long long code generation strategy reviewed.
   When using alloca, the generated code would crash/fail if optimizations
   were ON. Fixed.
   Pentium4 SSE2  back end started. It is not in this release yet.
   Bug in generation of long long constants fixed.
   The resource compiler had a limitation of strings of 255 characters. Fixed.
   The debugger has a display for the new XMM registers.
   The assembler has been modified to accept the new Pentium4 instructions.
   Mousewheel support has been added to Wedit.
   The documentation of the system has been extensively reviewed, to add all 
   pentium SSE2 instructions. See the assembler docs.
Sep 25:
   There were several inefficiencies in the code generation when a function
   a small structure was used. This has been corrected. Improvements are
   noticeable in the bignums.dll, that is much smaller now. Besides that dll
   was being shipped with debug info, what has been corrected.
Sep 21:
   Variable length arrays implemented. This allows you to write code like this:
   int fn(int a)
   {
   	int array[n];
	...
   }
   Improvements to the inline keyword implementation.
   Static character strings can now be of any length. The 16K limit has been 
   eliminated.
   Added "stdheaders.h" to the include directory, including the most frequently
   used standard include files.
Aug 28:
   rpcproxy.h would not compile in stand alone. Fixed.
   First implementation of the "inline" keyword
   Added htmlhelp.h to the distribution
Aug 9:
   A typo in w32incl.c (a missing "%") provoked that the assembler crashed when
   generating the code. Fixed.
   An error in the MULL machine description provoked the generation of mul
   (unsigned multiplication) instead of imul (signed multiplication). this
   happens only when the two input values were 32 bit integers and the 
   output should be 64 bits; Fixed
   Include file rasdlg.h was missing. Added to the distribution.
July 28:
   A .def file added to a dll project wouldn't be used when constructing the 
   makefile. Fixed.
Juli 26:
   Fixed problem in the spiller (compiler)
   Fixed diagnostics without line number info in resource compiler
   Fixed problem with multiplication and division of manifest constants in 
   resource compiler
   Fixed problem of *cbk.c in the IDE.
   Several bug fixes in the headers.
June 29:
   Fixed bug in the optimizer when you write:
	string[j++] = '/';
	string[j++] = '/';
   The optimizer was generating bad code when both those statements appeared
   one after the other.
   This bug in the optimizer made wedit reformat the comments incorrectly.
June 27:
   Fixed bug in the garbage collector that would make wedit crash. "Fixed" is
   actually too big for this. I just added a check for valid pointers, that's
   all.
   Fixed problem with coordinates that would make wedit crash under windows 95.
   If you give bogus coordinates to MoveWindow, Windows would freeze solid.
   When looking for the header file of a resource, the current directory is
   now reset to the sources directory. This allows you to skip a long series
   of button clicks to get to the correct directory or worst, to choose a wrong
   file.
   Fixed problem with the linker. When defining an alias in a definition file,
   the export directory of the executable was sorted wrong, and this made
   windows miss some valid exports.
June 25:
   Added left selection bar to the editor.
   Ctrl+End didn't work correctly. Fixed.
   Compiler wasn't optimizing all the possible occurrences of stack adjustments.
   Fixed.
June 23:
   The code generation for overloading == and != was wrong in the case 
   of integer constants. Fixed.
   The bignums library contained several errors. Fixed.
June 20:
   The compiler would reject: unsigned short a = (unsigned short)3.14; 
   Fixed.
   The compiler would generate wrong code for a stdcall function that
   returned a structure whose size is less than 9 bytes. The stack
   didn't get cleaned up, i.e. the _stdcall attribute was ignored.
   Fixed.
   The code management system (CMS) was taken away from the distribution.
   It is no longer part of wedit and will be distributed as a separate
   module.
June 10:
   Fixed problem with foreign characters in the resource editor.
May 27th:
   The compiler now will accept flexible array members at the end of a
   structure declaration.
   For instance:
	struct f { int a; char b[]; };
   Problem with the following code:
	unsigned short *p = L"abc"
                            L"def"
   corrected. The strings will be concatenated, as they should.
   Wedit now understands the pre-processor errors when in a macro expansion.
   A problem with the files toolbar fixed... the number of files was -1...
   Several crashes in wedit were fixed. For instance, if the files toolbar was
   active when no files were open, wedit would crash. Other crashes provoked
   by  clicking in other windows than the focus window were fixed.
   When using the "open all files" option, wedit would not test that the file
   to open wasn't a .lib or .obj file... Corrected.
May 23th:
   Wedit didn't handle correctly the Show definition option in the right button
   menus.
   Sample added to the e-mail distribution: Code to find the DirectX version
   installed in the machine.
   Added following header files to the distribution to support directx 8.
   d3d8.h dmdls.h d3dx8.h d3d8types.h d3d8caps.h d3dxmath.h d3dx8math.inl
   d3dx8core.h d3dx8tex.h d3dx8shape.h d3dx8mesh.h dplay8.h d3dx8effect.h
   dvoice.h
   Updated dsound.exp, dsetup.exp dinput8.exp.
   Added getdxversion to the e-mail samples distribution. This sample returns
   an integer between 0 and 8, telling you which version of directx you are 
   running.
   Added sample to draw a sprite to the e-mail distribution. First complex sample
   that compiles successfully with the new headers.
   Added dxerr.dll to the distribution. This exports the DXErrorMsg function.
   Added a documentation chapter for directx (23 pages).
   Updated other documentation (header files listings, for instance).
   There was a problem with functions with optional arguments. Under some 
   circumstances, the compiler would enter an infinite loop. Corrected.
   Documentation for AMD 3DNOW instructions now in the manual, together with
   the MMX instructions doc.
May 21th:
   When an overloaded function takes a long long argument, its "mangled" name 
   would be:
   int foo_long_long
   When an overloaded function takes two longs as arguments, its name would be:
   int foo_long_long
   Exactly identical. I corrected this bug...
   The browsegen utility wasn't writing correctly the prototypes when long
   long arguments were in the declaration. Fixed.
   The resource editor will not write absolute paths to the include header files 
   any more, and will assume the header is in the current directory or in the
   include path. Besides, the #include <windows.h> will be enclosed in a
   #ifdef __LCC__ to make it more portable.
   Directx8 support. This version fixes libraries and other problems with 
   directdraw support.
May 19th:
   Header file ws2tcpip.h was missing.
   Problems with overloaded functions: unsigned int wasn't accepted as a 
   substitute for int.
   Bug when calling through function pointers returning structures corrected.
May 15th:
   Maintenance release that correct the bugs of the previous one. Sorry... :-(
   For instance, when you press compile with a file open that is not in the
   current project, a dialog box would ask if you want to add it to the current
   project. If you pressed ADD, nothing happened. Corrected!
May 10th:
   MAJOR revamping of the resource editor.
   1: User defined resources should work now.
   2: International characters shouldn't get lost.
   3: Adding an icon/bitmap should work now.
   The values _HEX and _BLANK were wrong in ctype.h. Corrected, and rebuild libc.lib
May 5th:
   All references must be eliminated in an expression. The compiler wasn't doing
   this in the case of several references in an expression. Fixed.
   The documentation for printf was completely unusable do to bad formatting.
   Fixed.
   Problems in the menu editor. Solved problem of crash when the symbolic name
   of the menu was being edited.
   Crash when the "Properties" of string table were chosen solved.
   In the compiler, some intermediate expression that returned a reference
   weren't handled correctly.
   In the configuration tab you could move the embedded dialog boxes with the
   mouse. Corrected.
   The include path should be enclosed in quotes when there is a space in it.
   Corrected.
May 1st:
   First release of the improved RAD environment for lcc-win32. Many bugs in it
   were fixed, and a tighter integration with wedit is done.
   New index in the help file with all the standard library functions for quick
   reference.
Apr 20
   Bug in exception handling corrected. The compiler would save if there were 
   two __try blocks one right after the other.
   Corrected typo's in win.h and stdlib.h
   Infinite loop when importing a project. Corrected.
Apr 19
   Wedit uses the html format now for the online documentation, if available

Apr 14
   Operator overloding with = should work now
   Problems with the != operator corrected. Thanks to Sjouke Hamstra.
   imaxdiv function implemented
Apr 4:
  Problems with optional arguments fixed.
  LPNMTREEVIEW was missing in win.h

Mar 29:
  Problem with overloaded functions solved.
Mar 28:
  Missing definitions in wininet.h added.
  Bug that made the compiler choke when expecting a char pointer and finding 
  a char array when calling an operator function corrected.
Mar 25:
  Error with overloaded functions corrected.
Mar 24:
  Corrected bug in lcclib.exe: when the output file name was incorrect, it 
  would crash instead of showing an error message. Fixed.
Mar 22:
  Added optional arguments to the grammar. This feature is identical to the C++
  one: you write:
  int foo(int a,int b=2,int c = 3);
  "c" and "b" will be optional arguments, so you can make calls like:
   foo(1); // Compiler supplies foo(1,2,3);
   foo(1,78); //Compiler supplies foo(1,78,3);
Mar 19:
  Release of Berkeley DB for lcc-win32
Mar 14:
  Fixed problem in keyboard reconfiguration.
Mar 13:
  Output and input redirection work with the debuggers arguments. You can now
  write: >outfile in the debugger args, and the stdout of the program under 
  debug will be redirected to that file.
  matherr correction.
  long long problem solved (code generation)
Feb 19:
  Multi-threaded debugger. First release.
Feb 7:
  Problem in the asin atan, etc functions in the bignums library corrected.
   Problems with several types in the bignum lib header files fixed.
   Documentation for the bignums lib added to the user's manual.
   In the debugger fixed repeating of question: sources are newer than
   the executable.
   In the debugger the option for displaying memory using decimal notation did
   not work. Fixed.
Feb 3:
   Problem with the compiler and Unicode strings. Strings containing embedded
   zero bytes weren't emitted correctly. For instance the string "A\0BCD" would
   consist of only the unicode char A, stopping at the first zero. Fixed.
Feb 2:
   Fixed the tab key in the dialog editor when the test dialog is active. This
   wasn't working.
   Changed the font of all dialogs in wedit from "Helv" to MS Sans Serif, to
   cope with machines that do not have that font installed.
   Fixed (again) the accelerator table in the dialog editor. When creating a
   table the editor refused to accept the new table.
Jan 28:
   Put the macros with variable arguments modification in the compiler.
   Fixed other small issues in the user interface of Wedit: button's text, etc.
Jan 20:
   Fixed tchar.h _strnicmp instead of _strnicmp.
   Added an option to generate a Excel CSV in the "Audit" option of the analysis
   menu-group. This will generate an Excel table of all loaded functions, so
   including the line count, the size, etc etc.
   Improved the dialog box for the 'File relations" option.
   The print option of the metrics plot did not work. Fixed.
   Optimization bug with long longs fixed.
   Problem with int --> double comparison fixed in the code generator.
   The linker was generating a wrong size for the .reloc section. Fixed.
   Added the drawing of tick marks in the plotter for the software metrics
   module.
   
Jan 16:
   The versioning system did not take into account any extra files the user
   stored using the put command. Files would be stored but there wasn't any way
   of making a "get" afterwards. This is corrected now.
   The versioning system didn't handle extraneous files in the project file list
   and crashed with names like "tcconio.lib" for instance. The type of files
   that the versioning system understands has been limited to C,H,RC,DLG. Unless
   the user explicitly puts a file of another type, it will not be considered
   as a valid CMS file.
   A bad allocation error in the configuration dialog produced a "Overwritten
   block size 33" message. Fixed.
   The macros "max" and "min" were missing from stdlib.h
   
Jan 13:
   Packing of structures with bit-fields using #pragma pack(1) did not work 
   correctly. Fixed.
Jan 12:
   Problem with multiple selection in when selecting revisions in the 
   versioning system fixed.
   Background of diff dialog box changed to white. This makes text more visible.
   Introduced the software metrics option for a whole project. All files will 
   be measured.
   Introduced timing statistics for project and session, to be displayed in the
   General tab of the configuration wizard.
   A double quote too much caused the makefile generation to fail when 
   compiling for a library project. Fixed.
   Documentation for inp was missing.
Jan 09:
   Problems with printing: did not print for the second time. Fixed.
   Problems when generating code for the bool type. Fixed.
Jan 04:
   Still problems with makefile generation fixed.
   Bug with long long division fixed.
   Problem with // comments in the reformater solved
   Problem with bignums dll solved.
Jan 03:
   In some circumstances, when an unknown library function was used, pressing
   F8 (for trace) in the debugger would make the debugged program continue 
   no longer under debugger control. Fixed.
   When comparing two long longs, the stack was being left in a wrong state.
   The code generated by the compiler was missing two pop instructions.
Jan 02
   Bug in the optimizer with subtraction from long longs. Corrected.
   Bug in the optimizer with intermediate registers using two long long 
   expressions. Now the optimizer is almost disabled if any long long
   expressions appear in a function. The problem is that with only 6 registers
   available, any operation with two long longs uses almost all the CPU.
   Upgraded the setup utility to the new installer format.
   Corrected several bugs in the handling of pathnames with spaces. This will
   probably go on for a while sigh...
Dec 31:
   Fixed errors un bignum.h
   Fixed erros in stdlib.h concerning the _environ variable.
   Fixed erros in errno.h concerning the errlist variable.
   Compiler problem: When using intrinsics for sin or cos functions, it was
   impossible to take the address of those functions, since they are always
   inlined. Fixed.
   Fixed problem with the makefile in the buildlib that made installation fail.
   Fixed problem of embedded blanks in pathnames in the wizard, and the
   corresponding place in wedit when it calls it.
   Added code to test each include path for validity.
   Added prototypes in stdlib.h for _rotr, and _lrotr. Thanks to Peter Raddatz.
   When no project is defined, the menu items in the Project menu weren't
   grayed correctly. Fixed.
   The keyboard macro Ctrl+F could not be set. Corrected.
   Corrected bug of '\ ' (backslash followed by a space) in ddraw.h and several
   other header files.
   Fixed several small problems with the print dialog.
   Fixed problem of spaces in lcc path when calling buildlib in lcclnk.
   Fixed several problems with spaces in the lcc path (i.e. when you install 
   into "Program Files"). Make generation, and many utilities would fail.
   Fixed pedump, buildlib, browsegen, etc.
   Added documentation for the -Pn option to browsegen.
   Added feature "Import foreign library" to transform a .lib in the format
   used by Microsoft, to the format used in lcc.  This was added to wedit.
Dec 26:
   Added browse button to the grep dialog box.
   Added several pages technical description to lccwin32.doc specifying the
   workings of the project management, keyboard handling, screen redrawing, and
   others. This prompted some cleaning in the data structures within wedit.
   Some unused variables are gone, for instance.
   Suppressed the "Project" tab in the configuration wizard. The few valid
   fields were moved to the "general" tab.
   Added the System info tab to the configuration wizard, mainly for getting the
   details of the gc.
   Corrected problem in buildlib.exe when confronted with extremely long
   symbols. Now the maximum symbol name length is 512 characters! 
   Added lseeki64 and telli64 functions to the standard library to support
   files bigger than 2GB. Modified the corresponding on-line documentation.
   Revised the index of standard library functions to fill  missing functions 
   and type errors, adding those functions too.
   Added an index to lccwin32.doc. 
   Eliminated the "Browse function" menu item in the right button menu. It didn't
   bring any really important functionality.
   Fixed many bugs and problems in the revision control system. The "revisions"
   command would make wedit crash. The interface to several dialog boxes was
   simplified, eliminating unnecessary extra dialogs that appeared everywhere.
   The bookmark menu wasn't being grayed as it should when no bookmarks were
   defined.
   The accelerators for next bookmark did not work. Fixed.
   The prototypes writing has been moved from the small special purpose parser
   in wedit to the browsegen utility, that has improved the -P flag handling
   in the compiler. The -P flags looks like this:
   -P (or -P1) Write only global function prototypes.
   -P2 Write global protos and global data definitions (int foo, for instance)
   -P3 Write global protos, global data defs, and static protos.
   -P4 Write everything: global and local protos and data definitions.
   Wedit will remember this options for you. Just use the new Write protos
   option. By default, always the generated files will be loaded into the 
   editor.
   Updated netapi32.exp to changes in windows 2000 API.
   Eliminated support for asm files, int the editor, since this didn't work at 
   all, and it would be too much effort to support it correctly. Asm files
   can still be loaded but there will be no syntax coloring or finding the
   procedures in the file.
Dec 16:
   Linker wasn't recognizing comments within the .def files. Fixed. Comments
   start with a ';'. Fixed.
   Wedit wasn't recognizing relative paths when building a project.This has been
   improved...
   Wedit will generate now a dependencies file in the compilation directory 
   that is used by the workspace window to display the header files, and in the
   makefile generation process.
   The makefile generation process will not rescan all source files at each 
   time if at all possible. This speeds-up the makefile generation for big 
   projects.
   The workspace window would fail to open a file with relative paths
   specifications. Corrected.
   Updated online documentation for lcc options. (Thanks to lado brisar)
Dec 8:
   Linker bug fixed: the definitions file (.def) weren't working correctly.
   Eliminated the  #define of RtlZeroMemory as memset.
   The standard header wctype.h was added to the distribution as required by the
   standard.
   Tooltips were added to the resource editor's toolbars.
Dec 5:
   When a bit field is declared as unsigned long, the resulting type should be 
   unsigned, and not signed integer, as it was till now. Corrected. Thanks to 
   Pascal Haible.
   Under certain circumstances, the optimizer would alias several local 
   variables to the same register producing bad code. Corrected.
   GNU GDBM library added, with documentation, to the distribution.
Nov 27:
   TVINSERTSTRUCT and TVITEM were missing from win.h. Added
   Corrected problem with unsigned long longs and shifts.
   Corrected bug in the optimizer that produces an incorrect move instruction 
   in the assembler output
   Corrected problem with temporary variables of long longs.
Nov 23:
   Erasing a project in wedit didn't work correctly. Corrected.
   The complex functions library complex.lib has been updated to use ANSI-C-99
   function names.
   The complex library has been updated with acosh and other functions. A
   revision of the complex library is under way.
   i64toa didn't work correctly for some long long values. The same for atoll.
Nov 20:
   When the compiler sees if (0) { ...} the code will not be compiled. This
   optimization produces an error when there is a goto statement that points to
   a label in the excluded block. Corrected.
   Added header files to the workspace option of wedit.
   The resource compiler lrc.exe had a stupid limitation of 70K for rcdata
   resources. This has been eliminated.
   Bug in the optimizer corrected: under some circumstances, a cached register
   variable would be used when the value in the cache was wrong. Corrected with
   the help of planeta@iach.cz
   _Pragma() construct implemented. This is required by the C99 specifications
   page 159.
   The History command in wedit has now an accelerator of alt + left arrow.
Nov 15:
   A bug in the previous version (Nov 1) provoked a loop that made wedit use
   100% CPU time. Corrected.
   The compiler didn't correctly convert doubles to long longs and vice versa.
   The warning about signed/unsigned comparison was moved to a higher
   warning level.
   nspapi.h was incorrect? Added missing definitions.
   Corrected bug in selecting first column with the mouse
   Corrected bug in memory window in the debugger: the address field wasn't 
   incremented and there were several redrawing problems.
   Added _makepath to stdlib.h
   Added missing definition of STDMETHODIMP to objbase.h. Bug report
   contributed by lesbread@tomatoweb.com
   Fixed crash in the configuration wizard. (bug report from wpress@unis.ru)
   Introduced the History command in wedit.
Nov 1:
   The utility browsegen was broken. It didn't generate the browse file!
   The block indent code in wedit was interfering with replacement of the
   current selection: the tab key didn't work for replacing the selection.
   Generic functions extension: first implementation
   Corrected bug in wedit when you deleted (with the DEL key) a letter, UNDO
   did not work.
Oct 30:
   Problems with the installation program. Dolibs announces a non-existent 
   error. Corrected (I hope)
   Several bugs in code generation with long long corrected again. Another 
   problem with the optimizer solved.
Oct 28:
   Project workspace modifications. The project window appears at the left side
   of the editor. Projects aren't longer stored in the registry but in ascii 
   files. This makes the export and import options obsolete. A "migrate.exe" 
   utility is provided to move from the old registry format to the new one.
   The lcc.dll library is provided for people that want to use the compiler as 
   a dll.
   Problems with the ide crashing were solved.

Oct 1:
   Bug with boolean declarations corrected.
   XOR worked incorrectly for unsigned long longs.
Sep 29:
   Correction of problems when compiling "bool foo = true;"
   Two identical structure definitions will not provoke an error now. This means
   that
   typedef struct tagFoo { int a; int b; } FOO;
   typedef struct tagFoo { int a; int b; } FOO;
   will be accepted. The names of the structures and fields must be equal, and
   their types must be exactly equal.
   Correction of the profile problems. The profiler works again.
   
Sep 10:
   I have added a compilation driver, lc.exe, that will call the compiler, and
   if there are no errors, will automatically call the linker. Even if lcc-win32
   with Wedit has no real need for this, many users have complained that use
   lcc from the command line.
   Wedit: You can now indent a marked block with the TAB key, and move a
   block to the left with Shift tab.
   Error messages in the compiler improved for clarity.
   The tutorial has a dozen of pages more.
   The wizard can now generate a File open procedure to get an open file
   name. Documentation updated.
   Tested installing into folders with spaces in file names (i.e. Program files)
   Seems to work.
Sep 6:
   New header files added:
   wmistr.h
   guiddef.h
   evntrace.h
   Modified:
   tlhelp32.h (contained errors)
   stdlib.h: missing _wtoi definition and other functions
   win.h modified to put all tool help definitions in tlhelp32.h
   Bug fixes:
   Corrected error in version module when setting/unsetting the locks.
   Corrected error with proportional font in wedit.

Sep 4
1: A new check was done for spaces in filenames. This should work better now,
   since I was able to compile a project in
   F:\program files\Microsoft Platform Sdk\samples\dataaccess\odbc
             ^                        ^
   I had to modify the compiler, the resource compiler, and the IDE. I hope
   I haven't introduced any bugs...
2: I started compiling odbc with lcc-win32. New and updated files:
   sql.h sltypes.h sqlucode.h sqlext.h

Sep 2
1: The make utility accepts redirection of its output. You can then write 
   commands like: flex file.l >file.c
2: A bug in the project flags settings left always the debug information in
   the executables. Corrected.
3: The compiler choked with int i1,i2,i3; i1 = (long long)i1*i2/i3; This is
   corrected now.
4: AMD support for the 3DNOW instructions. The assembler accepts the 3DNOW
   instructions, the compiler has now intrinsics for using them, and the
   debugger shows the AMD floating MMX registers.
5: Added include file shlwapi.h to be able to execute the shell extensions
   API.Corrected several bugs in this part.
6: The debugger has a error window showing at each step the result of
   GetLastError().
7: Corrected the sample code in the shell extensions API documentation.
8: Compiler did not correctly compile right shifts for 64 bit longs.

Aug 17:
THE NASTY BUG OF WINDOWS 95 IS GONE!!!!!
Please windows 95 users download this version.
Other corrections within the resource editor.
Jul 31
------
1: Resource editor: Corrected the string table editor to use symbolic
   identifiers instead of bare numbers. This wasn't working before.
2: Resource editor: Corrected the string table editor to better check the
   values of the numeric IDs: tests for zero or negative IDs and rejects them.
3: Wedit:Problem with the search/find box crashing corrected.

July 10:
1: Yes, a new small problem in long longs. Damm is this difficult. But bugs
   are now difficult to find what is a measure of progress...
2: strtrim was missing from the stdlib functions. Corrected so that F1 will
   work with under wedit.
3: Wedit entered an infinite loop when the debugger found a long long type.
   Corrected.
   
July 9:
1: Problem with _stdcall function pointers hidden in typedefed function pointers
   is corrected. phew...
2: A new C-Tutorial is in the documentation file lccdoc.exe. It has already
   more than 100 pages and tries to explain the C language to the beginner
   starting with the traditional hello world program up to building a DLL.
   This is just the first version, more will follow.

July 2:
1: Problem with floating point intrinsic functions in the optimizer corrected.
2: Code with constructs like:
	if (0) {
		... statements...
	}
   will now not generate any code. Warnings are disabled (by default) when
   parsing the code that is not active.
3: Error reporting has been improved. Some "error cascades" are now avoided.
4: Problem in the IDE with some paths that contained nonsense is solved.
June 29:
-------
1: Users complained that the linker was mixing random data from RAM into the
   executables it built. This has been corrected, the linker now cleans up the
   space in the executable, as windows-nt or windows-2000 makes automatically.
   This isn't done by windows 98 or windows 95, so the linker will do that
   instead. This has been a cause of concern by certain users, what prompted
   an immediate upgrade.
2: A saga without end: unsigned long long support was still missing some ops.
   (several nested levels of AND or ORs of long longs provoked trouble). What
   is reassuring is these bugs get increasingly sophisticated, i.e. simple
   expressions are well covered already.
3: Documentation for the functions in tcconio.lib are now online.
4: The optimizer will now recognize
             if (0) {
                    // ... some code ...
             }
   and absolutely no code will be generated for it. By default, no warnings
   will be issued in this code, unless you compile with the -A option.

June 19: 

1: Problems appeared when a function had a reference argument. The reference
   bit was getting lost in comparing the function proto with the actual defined
   function arguments. This has been corrected.
2: The bignum (arbitrary precision) library has been added.
   See the documentation (user's manual) or \lcc\include\bignums.h
3: Some problems with the "strlen" intrinsic were corrected. This provoked a
   crash in the resource editor.

June 17
-------
1: Forgot the intrinsic for unsigned long division in libc.lib. This would
   provoke that the linker complained about an undefined.
2: The bignum (arbitrary precision) library has been added.
   See the documentation (user's manual) or \lcc\include\bignums.h
3: Some problems with the "strlen" intrinsic were corrected. This provoked a
   crash in the resource editor.

June 17
-------
1: Forgot the intrinsic for unsigned long division in libc.lib. This would
   provoke that the linker complained about an undefined.
-------
1: Forgot the intrinsic for unsigned long division in libc.lib. This would
   provoke that the linker complained about an undefined.
2: Unsigned long long further problems with some conversions solved.
3: The promotion of some built-in types wasn't working with operator overloading
   Now those are correctly promoted. The operator '%' wasn't being recognized
   for subclassing. Corrected.
3: Updated a bit the doc.
4: The bignum (infinite precision) library is almost working, but I decided
   not to include it in this one.


June 13
-------
1: This is a major release: it features for the first time the memory manager
   developed by Hans Boehm. (gc). See the documentation (user's manual is
   updated) or the online docs (go to index and choose memory manager).
2: Problems with the unsigned long longs (yes, I know, I promised that there 
   would be no more bugs :-) have been solved, extensive test suite for them
   written. Well this time I do not promise anything but I am surely more
   confident in them. Thanks to Pascal Haible.
3: Fixed a buffer overflow in the macro expansion. Macros can get quite long
   when expanded, as I found out...
4: Fixed the alignment problems in the linker and in the assembler, that would
   prevent Hans Boehm's gc to run. This cost me weeks of development. Now is
   (finally) working. Please use only *THIS* version of the system with the gc.
   It will NOT RUN in earlier versions because of this alignment problems.


June 4
----
1: Added macros for the debug info of the debugger. Macro support doesn't
   included expansion, the definition however, is now included in the shipped
   debug information. The debugger will display the definition of the macro as a
   character string.
2: The C-FAQ has been added to the e-mail distribution, and is available in
    ftp://ftp.cs.virginia.edu/lcc-win32 and
    ftp://ps.qss.cz/lcc

May 28
-----
1: The compiler has been updated to recognize the boolean types. The unsigned
   long long problems of previous versions have  been corrected.
2: The IDE is now able to export projects with less problems...
3: Several small upgrades to the header files.
4: regexp.lib: a regular expression package has been added to the standard
   distribution.
5: fdlibm.lib, with more floating point functions has been added to the
   distribution.
6: Documentation updates for the operator syntax, and the new libraries.
7: The bug in the divisor being stored in register EDX, and provoking a
   division by zero error has been corrected.
8: Sometimes when proposing the missing libraries to a link, Wedit would crash.
   I hope this doesn't happen any more. A wandering NULL pointer was shot down.


May 15 2000
1: Operator overloading and references are introduced. This is a major revision
of the compiler. The documentation has been updated with a new file 
"operators.doc" that describes the new syntax, the rationale etc.
2: When the optimizer was on, sometimes a register was being lost in a misguided
   optimization effort. this happened when an assignment was being done to a
   register variable.

May 5 2000
---------
1: Several odd bugs in code generation, specially in the optimizer were corrected.
   They appeared when compiling Eiffel code and concerned floating point comparisons
   with integer values, and trashing a value in a register variable.
2: The IDE didn't recognize de utilities command anymore. Corrected.
1: Corrected a bug in the error message display: if the name of your source
   file happened to be the same as the name the directory you were in, the file name
   would be truncated to "c".
2: Corrected problems in the c preprocessor with foreign language code pages.
3: Corrected a code generation error in the optimizer concerning some cases
   of incorrect oring with immediate constants.
4: Corrected a code generation error that involved a bad register allocation for 
   intrinsic calls.

Documentation:
--------------
1 Added documentation for the way you add assembler macros to the compiler as
  "intrinsic" pseudo-functions.

Header files and Libraries:
---------------------------
Added libraries:
    hlink.exp and hlink.lib import libraries
    resutils.exp resutils.lib
    iprop.exp iprop.lib
    icmui.exp icmui.lib

Added header files:
     resapi.h
     clusapi.h
Corrected/updated files:
      windows.h (this is a long runner...:-)
      stddef.h

Wedit
-----
1. Corrected problem with tcconio.lib always being added to the libraries.
2. Bit fields are now supported in the debugger
3. Increased capacity for error messages in the error message buffer that was
   overflowing when too many warnings appeared in compilation.
4. The link command called make, rebuilding the objects. This has been corrected,
   and now only the link will be performed.
5. The problem with the add/delete files dialog box that required pressing
   two times the "validate" option has been corrected.
6. Wedit will now detect a missing library automatically, if the needed API
   is in the list of known APIs. That list has now something like 10500 entries.
   This list will be now generated automatically by the installation program
   and will be placed in \lcc\lib\apilist.txt.


17 Mai 1999:
            Bug when closing a project that has no name. Fixed.
            Bug when printing a function tree. The AbortProc goes into an infinite
            loop. Corrected (edit2.c)

new files added to the include directory:
adshlp.h snmp.h mqmail.h psapi.h ws2spi.h mq.h lmdfs.h cchannel.h midles.h nspapu.h svrapi.h
wfext.h mapiform.h digitalv.h lmat.h tnef.h imessage.h winnls.h winnls32.h winable.h tom.h
time.h rasshost.h confpriv.h tapi3if.h iads.h mqoai.h security.h sspi.h secext.h intshcut.h
lccpath.h
9 Mai 99:
            Fixed problems with the tree drawing functionality that would get
            lost if it found a function definition containing a brace in the
            same line that the function definition started, i.e.:
            int foo() {
            The link tab provoked a spurious 'project has changed' warning. This
            is because it didn't ignore case when comparing the old contents
            of the libraries and the new ones. Since I did an strlwr at the
            start, this would always provoke a spurious message the first
            time the tab was called. Corrected.
            Added support for stack integrity checking when the debug level is 4.
8 Mai 99:   Wedit debugger tab didn't read the starting dir correctly.
            This code made the compiler trap. Corrected.
            typedef int my_int;
            typedef unsigned my_int my_unsigned_int; // <-- the error occurs in this line 
7 Mai 1999: Added the lm*.h (lan manager) header files
            lrc was crashing when called with a special rc file. Corrected.
            inb and outb were wrong. C library corrected
            Resource editor doesn't screw up files when the 'new' project
            is chosen
            Some strings in the C code generated by the resource editor were
            wrong.
            Updated documentation for the changes in wedit's menu.
6 Mai 1999: Wedit understands now paths with embedded spaces
            crtdll.exp was wrong: is syslist and not syslist_dll. Corrected.
5 Mai 1999: Changed the menus, eliminating the 'Options' menu.

