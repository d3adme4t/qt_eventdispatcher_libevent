# qt_eventdispatcher_libevent [![Build Status](https://secure.travis-ci.org/sjinks/qt_eventdispatcher_libevent.png)](http://travis-ci.org/sjinks/qt_eventdispatcher_libevent)

libevent-based event dispatcher for Qt

## Features
* very fast :-)
* compatibile with Qt 4 (Qt 4.2+) and Qt 5
* does not use any private Qt headers
* passes Qt 4 and Qt 5 event dispatcher, event loop, timer and socket notifier tests


## Unsupported Features
* `QSocketNotifier::Exception` (libevent offers no support for this)
* Qt 5/Windows only: `QWinEventNotifier` is not supported (`registerEventNotifier()` and `unregisterEventNotifier()` functions
are currently implemented as stubs; libevent does not natively support Windows events and addition of the support
to the event dispatcher will mean a completely different event loop code for Windows).


## Requirements
* libevent >= 2.0.0 (the code seems to work with libevent 1.4.x but this has not been tested much — but the Qt tests are successfully passed though)
* Qt >= 4.2 (tests from tests-qt4 were run only on Qt 4.8.x, 4.5.4, 4.3.0, 4.2.1)


## Build

```
cd src
qmake
make
```

Replace `make` with `nmake` if your are using Microsoft Visual C++.

The above commands will generate the static library and `.prl` file in `../lib` directory.


## Install

After completing Build step run

*NIX:
```
sudo make install
```

Windows:
```
nmake install
```

For Windows this will copy `eventdispatcher_libevent.h` and `eventdispatcher_libevent_config.h` to `../lib` directory.
For *NIX this will install eventdispatcher_libevent.h to `/usr/include`, `libeventdispatcher_libevent.a` and `libeventdispatcher_libevent.prl` to `/usr/lib`, `eventdispatcher_libevent.pc` to `/usr/lib/pkgconfig`.


## Usage (Qt 4)

Simply include the header file and instantiate the dispatcher in `main()`
before creating the Qt application object.

```c++
#include "eventdispatcher_libevent.h"
    
int main(int argc, char** argv)
{
    EventDispatcherLibEvent dispatcher;
    QCoreApplication app(argc, argv);

    // ...

    return app.exec();
}
```

And add these lines to the .pro file:

```
unix {
    CONFIG    += link_pkgconfig
    PKGCONFIG += eventdispatcher_libevent
}
else:win32 {
    include(/path/to/qt_eventdispatcher_libevent/lib/eventdispatcher_libevent.pri)
}
```

or

```
HEADERS += /path/to/eventdispatcher_libevent.h
LIBS    += -L/path/to/library -leventdispatcher_libevent
```


## Usage (Qt 5)

Simply include the header file and instantiate the dispatcher in `main()`
before creating the Qt application object.

```c++
#include "eventdispatcher_libevent.h"

int main(int argc, char** argv)
{
    QCoreApplication::setEventDispatcher(new EventDispatcherLibEvent);
    QCoreApplication app(argc, argv);

    // ...

    return app.exec();
}
```

And add these lines to the .pro file:

```
unix {
    CONFIG    += link_pkgconfig
    PKGCONFIG += eventdispatcher_libevent
}
else:win32 {
    include(/path/to/qt_eventdispatcher_libevent/lib/eventdispatcher_libevent.pri)
}
```

or

```
HEADERS += /path/to/eventdispatcher_libevent.h
LIBS    += -L/path/to/library -leventdispatcher_libevent
```

Qt 5 allows to specify a custom event dispatcher for the thread:

```c++
QThread* thr = new QThread;
thr->setEventDispatcher(new EventDispatcherLibEvent);
```


## Interesting Facts

`EventDispatcherLibEvent` is more compatible with Qt 4.2.x and 4.3.x than the native UNIX event dispatcher from those Qt's.

For example, Qt 4.2.1 fails `tst_QTimer::livelock(zero timer)` and `tst_QTimer::livelock(non-zero timer)` tests
(`'tester.postEventAtRightTime' returned FALSE`) and hangs in `tst_QEventLoop::processEventsExcludeTimers` test
but `EventDispatcherLibEvent` passes them all!

It should, however, be noted that these tests were taken from Qt 4.8 :-)
