Contribution Guide
==================

-  `Bug Reports <#bug-reports>`__
-  `Core Development Discussion <#core-development-discussion>`__
-  `Which Branch? <#which-branch>`__
-  `Security Vulnerabilities <#security-vulnerabilities>`__
-  `Coding Style <#coding-style>`__

 ## Bug Reports

To encourage active collaboration, Laravel strongly encourages pull
requests, not just bug reports. "Bug reports" may also be sent in the
form of a pull request containing a failing unit test.

However, if you file a bug report, your issue should contain a title and
a clear description of the issue. You should also include as much
relevant information as possible and a code sample that demonstrates the
issue. The goal of a bug report is to make it easy for yourself - and
others - to replicate the bug and develop a fix.

Remember, bug reports are created in the hope that others with the same
problem will be able to collaborate with you on solving it. Do not
expect that the bug report will automatically see any activity or that
others will jump to fix it. Creating a bug report serves to help
yourself and others start on the path of fixing the problem.

The Laravel source code is managed on Github, and there are repositories
for each of the Laravel projects:

-  `Laravel Framework <https://github.com/laravel/framework>`__
-  `Laravel Application <https://github.com/laravel/laravel>`__
-  `Laravel Documentation <https://github.com/laravel/docs>`__
-  `Laravel Cashier <https://github.com/laravel/cashier>`__
-  `Laravel Envoy <https://github.com/laravel/envoy>`__
-  `Laravel Homestead <https://github.com/laravel/homestead>`__
-  `Laravel Homestead Build
   Scripts <https://github.com/laravel/settler>`__
-  `Laravel Website <https://github.com/laravel/laravel.com>`__
-  `Laravel Art <https://github.com/laravel/art>`__

 ## Core Development Discussion

Discussion regarding bugs, new features, and implementation of existing
features takes place in the ``#laravel-dev`` IRC channel (Freenode).
Taylor Otwell, the maintainer of Laravel, is typically present in the
channel on weekdays from 8am-5pm (UTC-06:00 or America/Chicago), and
sporadically present in the channel at other times.

The ``#laravel-dev`` IRC channel is open to all. All are welcome to join
the channel either to participate or simply observe the discussions!

 ## Which Branch?

**All** bug fixes should be sent to the latest stable branch. Bug fixes
should **never** be sent to the ``master`` branch unless they fix
features that exist only in the upcoming release.

**Minor** features that are **fully backwards compatible** with the
current Laravel release may be sent to the latest stable branch.

**Major** new features should always be sent to the ``master`` branch,
which contains the upcoming Laravel release.

If you are unsure if your feature qualifies as a major or minor, please
ask Taylor Otwell in the ``#laravel-dev`` IRC channel (Freenode).

 ## Security Vulnerabilities

If you discover a security vulnerability within Laravel, please send an
e-mail to Taylor Otwell at taylorotwell@gmail.com. All security
vulnerabilities will be promptly addressed.

 ## Coding Style

Laravel follows the
`PSR-0 <https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md>`__
and
`PSR-1 <https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md>`__
coding standards. In addition to these standards, the following coding
standards should be followed:

-  The class namespace declaration must be on the same line as
   ``<?php``.
-  A class' opening ``{`` must be on the same line as the class name.
-  Functions and control structures must use Allman style braces.
-  Indent with tabs, align with spaces.

