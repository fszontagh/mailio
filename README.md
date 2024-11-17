
# mailio #

[中文文档](README_zh.md)

*mailio* is a cross platform C++ library for MIME format and SMTP, POP3 and IMAP protocols. It is based on the standard C++ 17 and Boost library.


# Examples #

To send a mail, one has to create `message` object and set it's attributes as author, recipient, subject and so on. Then, an SMTP connection
is created by constructing `smtp` (or `smtps`) class. The message is sent over the connection:

```cpp
message msg;
msg.from(mail_address("mailio library", "mailio@gmail.com"));
msg.add_recipient(mail_address("mailio library", "mailio@gmail.com"));
msg.subject("smtps simple message");
msg.content("Hello, World!");

smtps conn("smtp.gmail.com", 587);
conn.authenticate("mailio@gmail.com", "mailiopass", smtps::auth_method_t::START_TLS);
conn.submit(msg);
```

To receive a mail, a `message` object is created to store the received message. Mail can be received over POP3 or IMAP, depending of mail server setup.
If POP3 is used, then instance of `pop3` (or `pop3s`) class is created and message is fetched:

```cpp
pop3s conn("pop.mail.yahoo.com", 995);
conn.authenticate("mailio@yahoo.com", "mailiopass", pop3s::auth_method_t::LOGIN);
message msg;
conn.fetch(1, msg);
```

Receiving a message over IMAP is analogous. Since IMAP recognizes folders, then one has to be specified, like *inbox*:

```cpp
imaps conn("imap.gmail.com", 993);
conn.authenticate("mailio@gmail.com", "mailiopass", imap::auth_method_t::LOGIN);
message msg;
conn.fetch("inbox", 1, msg);
```

More advanced features are shown in `examples` directory, see below how to compile them.

Note for Gmail users: it might be needed to [register](https://support.google.com/accounts/answer/6010255) *mailio* as a trusted application. Follow the
[Gmail instructions](https://support.google.com/accounts/answer/3466521) to add it and use the generated password for all three protocols.

Note for Zoho users: if 2FA is turned on, then instead of the primary password, the application password must be used. Follow
[Zoho instructions](https://www.zoho.com/mail/help/adminconsole/two-factor-authentication.html#alink5) to add *mailio* as trusted application and use the
generated password for all three protocols.


# Setup #

*mailio* library is supposed to work on all platforms supporting C++ 17 compiler, Boost 1.81 or newer and CMake build tool. The platforms tested so far are
Linux, Windows, FreeBSD, MacOS, Cygwin, MinGW and the compilers are Gcc, Microsoft Visual C++ and Clang.

There are several ways to build *mailio*: by cloning the [repo](https://github.com/karastojko/mailio.git) and using Cmake, by using Vcpkg or
[xmake](https://xmake.io).


## CMake ##

Ensure that OpenSSL, Boost and CMake are in the path. If they are not in the path, one could use CMake options `-DOPENSSL_ROOT_DIR`, `-DBOOST_ROOT` and
`Boost_INCLUDE_DIRS` to set them. Boost must be built with the OpenSSL support. If it cannot be found in the path, set the path explicitly via `library-path`
and `include` parameters of `b2` script (after `bootstrap` finishes). Both static and dynamic libraries should be built in the `build` directory.

If one wants to specify a non-default installation directory, say `/opt/mailio`, then the option `-DCMAKE_INSTALL_PREFIX` should be used. If a user does not have
privileges for the default directories, then it must specify one by using this CMake variable.

Other available options are `BUILD_SHARED_LIBS` (whether a shared or static library shall be built, by default a shared lib is built),
`MAILIO_BUILD_DOCUMENTATION` (if Doxygen documentation is generated, by default is on) and `MAILIO_BUILD_EXAMPLES` (if examples are built, by default is on).


### Linux, FreeBSD, MacOS, Cygwin ###

From the terminal go into the directory where the library is downloaded to, and execute:
```
mkdir build
cd ./build
cmake ..
make install
```
Installing the project ensures that the test project has also the auxiliary files copied, required by several tests.


### Microsoft Windows/Visual Studio ###

From the command prompt go into the directory where the library is downloaded, and execute:
```
mkdir build
cd .\build
cmake ..
```
A solution file will be built, open it from Visual Studio and build the project. To install it, build the `INSTALL` project of the *mailio* solution, it copies
also auxiliary files required by several tests.


### Microsoft Windows/MinGW ###

Open the command prompt by using the `open_distro_window.bat` script, and execute:
```
mkdir build
cd .\build
cmake.exe .. -G "MinGW Makefiles"
make install
```


## Vcpkg ##

Install [Vcpkg](https://github.com/microsoft/vcpkg) and run:
```
vcpkg install mailio
```
Tests are not available as an option in this case. Use the CMake way to build them.


# Features #

* Recursive formatter and parser of the MIME message.
* MIME message recognizes the most common headers like subject, recipients, content type and so on.
* Message body encodings that are supported: Seven bit, Eight bit, Binary, Base64 and Quoted Printable.
* Header encodings that are supported: ASCII, UTF-8, Base64, Quoted Printable.
* Header attributes encodings that are supported: ASCII, Base64, Quoted Printable, Percent.
* String charset configurable for headers and their attributes.
* All media types are recognized, including MIME message embedded within another message.
* MIME message has configurable line length policy and strict mode for parsing.
* SMTP implementation with message sending. Both plain and SSL (including START TLS) versions are available.
* POP3 implementation with message receiving and removal, getting mailbox statistics. Both plain and SSL (including START TLS) versions are available.
* IMAP implementation with message receiving, removal and search, getting mailbox statistics, managing folders. Both plain and SSL (including START TLS)
  versions are available.


# Issues and improvements #

The library is tested on valid mail servers, so probably there are negative test scenarios that are not covered by the code. In case you find one, please
contact me. Here is a list of issues known so far and planned to be fixed in the future.

* MIME header attributes not fully implemented.
* IMAP supports only ASCII folder names.
* IMAP lacks the idle support.
* Editing parts of a message.
* IMAP flags.
* Asynchronous I/O.
* More descriptive error messages.
* External initialization of the SSL context.


# Contributors #

Thanks to all people who contribute to the project by improving the source code, report problems and propose new features. Here is a list from the Git history,
in case I missed someone please let me know.

* [Trevor Mellon](https://github.com/TrevorMellon): CMake build scripts.
* [Kira Backes](mailto:kira.backes[at]nrwsoft.de): Fix for correct default message date.
* [sledgehammer_999](mailto:hammered999[at]gmail.com): Replacement of Boost random function with the standard one.
* [Paul Tsouchlos](mailto:developer.paul.123[at]gmail.com): Modernizing build scripts.
* [Anton Zhvakin](mailto:a.zhvakin[at]galament.com): Replacement of deprecated Boost Asio entities.
* [terminator356](mailto:termtech[at]rogers.com): IMAP searching, fetching messages by UIDs.
* [Ilya Tsybulsky](mailto:ilya.tsybulsky[at]gmail.com): MIME parsing and formatting issues. UIDL command for POP3.
* [Ayaz Salikhov](https://github.com/mathbunnyru): Conan packaging.
* [Tim Lukas Harken](tlh[at]tlharken.de): Removing compilation warnings.
* [Rainer Sabelka](mailto:saba[at]sabanet.at]): SMTP server response on accepting a mail.
* [David Garcia](mailto:david.garcia[at]antiteum.com): Vcpkg port.
* [ImJustStokedToBeHere](https://github.com/ImJustStokedToBeHere): Typo error in IMAP.
* [lifof](mailto:youssef.beddad[at]gmail.com): Support for the MinGW compilation.
* [Canyon E](https://github.com/canyone2015): IMAP folder delimiter static variable issue.
* [ostermal](https://github.com/ostermal): Bug with the horizontal tab in MIME headers.
* [MRsoymilk](mailto:313958485[at]qq.com): Bug in the sending attachment example.
* [Don Yihtseu](https://github.com/tsurumi-yizhou): Add Chinese ReadMe.
* [Leonhard Kipp](mailto:Leonhard.Kipp@ppro.com): Proper way to build the shared library. Message formatting options.
* [Orchistro](https://github.com/orchistro): Improving CMake build script.


# References #

* [RFC 822](http://www.rfc-editor.org/rfc/rfc822): Standard for the Format of ARPA Internet Text Messages.
* [RFC 1939](http://www.rfc-editor.org/rfc/rfc1939): Post Office Protocol - Version 3
* [RFC 2045](http://www.rfc-editor.org/rfc/rfc2045): Multipurpose Internet Mail Extensions (MIME) Part One: Format of Internet Message Bodies
* [RFC 2046](http://www.rfc-editor.org/rfc/rfc2046): Multipurpose Internet Mail Extensions (MIME) Part Two: Media Types
* [RFC 2047](http://www.rfc-editor.org/rfc/rfc2047): MIME (Multipurpose Internet Mail Extensions) Part Three: Message Header Extensions for Non-ASCII Text
* [RFC 2177](http://www.rfc-editor.org/rfc/rfc2177): IMAP4 IDLE command
* [RFC 2183](http://www.rfc-editor.org/rfc/rfc2183): Communicating Presentation Information in Internet Messages: The Content-Disposition Header Field
* [RFC 2231](http://www.rfc-editor.org/rfc/rfc2231): MIME Parameter Value and Encoded Word Extensions: Sets, Languages, and Continuations
* [RFC 2449](http://www.rfc-editor.org/rfc/rfc2449): Extension Mechanism
* [RFC 3501](http://www.rfc-editor.org/rfc/rfc3501): Message Access Protocol - Version 4rev1
* [RFC 5321](http://www.rfc-editor.org/rfc/rfc5321): Simple Mail Transfer Protocol
* [RFC 5322](http://www.rfc-editor.org/rfc/rfc5322): Internet Message Format
* [RFC 5987](http://www.rfc-editor.org/rfc/rfc5987): Character Set and Language Encoding for Hypertext Transfer Protocol (HTTP) Header Field Parameters
* [RFC 6532](http://www.rfc-editor.org/rfc/rfc6532): Internationalized Email Headers


# Contact #

In case you find a bug, please drop me a mail to contact (at) alepho.com. Since this is my side project, I'll do my best to be responsive.
