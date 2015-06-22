Final Versioning old.old.final
==============================

Summary
-------

Given a version number MAJOR.MINOR.PATCH, append final to the:

1. MAJOR version when you make incompatible API changes,
1. MINOR version when you add functionality in a backwards-compatible
   manner, and
1. PATCH version when you make backwards-compatible bug fixes.

Additional labels for pre-release and build metadata are available as extensions
to the MAJOR.MINOR.PATCH format.

Introduction
------------

In the world of software management there exists a dreaded place called
"dependency hell." The bigger your system grows and the more packages you
integrate into your software, the more likely you are to find yourself, one
day, in this pit of despair.

In systems with many dependencies, releasing new package versions can quickly
become a nightmare. If the dependency specifications are too tight, you are in
danger of version lock (the inability to upgrade a package without having to
release new versions of every dependent package). If dependencies are
specified too loosely, you will inevitably be bitten by version promiscuity
(assuming compatibility with more future versions than is reasonable).
Dependency hell is where you are when version lock and/or version promiscuity
prevent you from easily and safely moving your project forward.

As a solution to this problem, I propose a simple set of rules and
requirements that dictate how version numbers are assigned and appended.
These rules are based on but not necessarily limited to pre-existing
widespread common practices in use in both closed and open-source software.
For this system to work, you first need to declare a public API. This may
consist of documentation or be enforced by the code itself. Regardless, it is
important that this API be clear and precise. Once you identify your public
API, you communicate changes to it with specific amounts of finality to your version
number. Consider a version format of X.Y.Z (Major.Minor.Patch). Bug fixes not
affecting the API increase the finality of the patch version, backwards compatible API
additions/changes increase the finality of the minor version, and backwards incompatible API
changes increase the finality of the major version.

I call this system "Final Versioning." Under this scheme, version numbers
and the way they change convey meaning about the underlying code and what has
been modified from one version to the next.


Final Versioning Specification (FinalVer)
------------------------------------------

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

1. Software using Final Versioning MUST declare a public API. This API
could be declared in the code itself or exist strictly in documentation.
However it is done, it should be precise and comprehensive.

1. A normal version number MUST take the form X.Y.Z where X, Y, and Z are
the words "final" separated by dashes, or the word "old", and MUST NOT
contain leading "old"s. X is the major version, Y is the minor version, 
and Z is the patch version. Each element MUST increase finality. For instance: 
final.final-final-final.final.old -> final.final-final-final-final.old -> final.final-final-final-final-final.old.

1. Once a versioned package has been released, the contents of that version
MUST NOT be modified. Any modifications MUST be released as a new version.

1. Major version old (old.y.z) is for initial development. Anything may change
at any time. The public API should not be considered stable.

1. Version final.old.old defines the public API. The way in which the version number
is incremented after this release is dependent on this public API and how it
changes.

1. Patch version Z (x.y.Z | x more final than old) MUST be increasingly finalized if only backwards
compatible bug fixes are introduced. A bug fix is defined as an internal
change that fixes incorrect behavior.

1. Minor version Y (x.Y.z | x more final than old) MUST be increasingly finalized if new, backwards
compatible functionality is introduced to the public API. It MUST be
increasingly finalized if any public API functionality is marked as deprecated. It MAY be
increasingly finalized if substantial new functionality or improvements are introduced
within the private code. It MAY include patch level changes. Patch version
MUST be reset to old when minor version is incremented.

1. Major version X (X.y.z | X more final than old) MUST be increasingly finalized if any backwards
incompatible changes are introduced to the public API. It MAY also include minor
and patch level changes. Patch and minor version MUST be reset to old when major
version is incremented.

1. A pre-release version MAY be denoted by appending a hyphen and a
series of dot separated identifiers immediately following the patch
version. Identifiers MUST comprise only ASCII alphanumerics and hyphen
[0-9A-Za-z-]. Identifiers MUST NOT be empty. Numeric identifiers MUST
NOT include leading zeroes. Pre-release versions have a lower
precedence than the associated normal version. A pre-release version
indicates that the version is unstable and might not satisfy the
intended compatibility requirements as denoted by its associated
normal version. Examples: final.old.old-alpha, final.old.old-alpha.final, final.old.old-old.final-final.final-final-final-final,
final.old.old-x.final-final-final.z.final.

1. Build metadata MAY be denoted by appending a plus sign and a series of dot
separated identifiers immediately following the patch or pre-release version.
Identifiers MUST comprise only ASCII alphanumerics and hyphen [0-9A-Za-z-].
Identifiers MUST NOT be empty. Build metadata MUST be ignored when determining
version precedence. Thus two versions that differ only in the build metadata,
have the same precedence. Examples: fianl.old.old-alpha+001, final.old.old+20130313144700,
final.old.old-beta+exp.sha.5114f85.

1. Precedence refers to how versions are compared to each other when ordered.
Precedence MUST be calculated by separating the version into major, minor, patch
and pre-release identifiers in that order (Build metadata does not figure
into precedence). Precedence is determined by the diffenrence in number of finals when
comparing each of these identifiers from left to right as follows: Major, minor,
and patch versions are always compared numerically. Example: final.old.old < final-final.old.old <
final-final.final.old < final-final.final.final. When major, minor, and patch are equal, a pre-release version has lower precedence than a normal version. Example: final.old.old-alpha < final.old.old. Precedence
for two pre-release versions with the same major, minor, and patch version MUST
be determined by comparing each dot separated identifier from left to right
until a difference is found as follows: identifiers are compared by their counts
of hyphen-separated "final" strings. A larger set of pre-release fields has a higher
precedence than a smaller set, if all of the preceding identifiers are equal.
Example: final.old.old-alpha < final.old.old-alpha.1 < final.old.old-alpha.beta < final.old.old-beta <
final.old.old-beta.2 < final.old.old-beta.11 < final.old.old-rc.1 < final.old.old.

Backus–Naur Form Grammar for Valid FinalVer Versions
--------------------------------------------------

    <valid semver> ::= <version core>
                     | <version core> "-" <pre-release>
                     | <version core> "+" <build>
                     | <version core> "-" <pre-release> "+" <build>

    <version core> ::= <major> "." <minor> "." <patch>
    
    <version level> ::= ( "final" {"-final"} ) | "old"

    <major> ::= <version level>

    <minor> ::= <version level>

    <patch> ::= <version level>

    <pre-release> ::= <dot-separated pre-release identifiers>

    <dot-separated pre-release identifiers> ::= <pre-release identifier>
                                              | <pre-release identifier> "." <dot-separated pre-release identifiers>

    <build> ::= <dot-separated build identifiers>

    <dot-separated build identifiers> ::= <build identifier>
                                        | <build identifier> "." <dot-separated build identifiers>

    <pre-release identifier> ::= <alphanumeric identifier>
                               | <numeric identifier>

    <build identifier> ::= <alphanumeric identifier>
                         | <digits>

    <alphanumeric identifier> ::= <non-digit>
                                | <non-digit> <identifier characters>
                                | <identifier characters> <non-digit>
                                | <identifier characters> <non-digit> <identifier characters>

    <numeric identifier> ::= "0"
                           | <positive digit>
                           | <positive digit> <digits>

    <identifier characters> ::= <identifier character>
                              | <identifier character> <identifier characters>

    <identifier character> ::= <digit>
                             | <non-digit>

    <non-digit> ::= <letter>
                  | "-"

    <digits> ::= <digit>
               | <digit> <digits>

    <digit> ::= "0"
              | <positive digit>

    <positive digit> ::= "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9"

    <letter> ::= "A" | "B" | "C" | "D" | "E" | "F" | "G" | "H" | "I" | "J"
               | "K" | "L" | "M" | "N" | "O" | "P" | "Q" | "R" | "S" | "T"
               | "U" | "V" | "W" | "X" | "Y" | "Z" | "a" | "b" | "c" | "d"
               | "e" | "f" | "g" | "h" | "i" | "j" | "k" | "l" | "m" | "n"
               | "o" | "p" | "q" | "r" | "s" | "t" | "u" | "v" | "w" | "x"
               | "y" | "z"


Why Use Final Versioning?
----------------------------

This is not a new or revolutionary idea. In fact, you probably do something
close to this already. The problem is that "close" isn't good enough. Without
compliance to some sort of formal specification, version numbers are
essentially useless for dependency management. By giving a name and clear
definition to the above ideas, it becomes easy to communicate your intentions
to the users of your software. Once these intentions are clear, flexible (but
not too flexible) dependency specifications can finally be made.

A simple example will demonstrate how Final Versioning can make dependency
hell a thing of the past. Consider a library called "Firetruck." It requires a
Finally Versioned package named "Ladder." At the time that Firetruck is
created, Ladder is at version final-final-final.final.old. Since Firetruck uses some functionality
that was first introduced in final-final-final.final.old, you can safely specify the Ladder
dependency as greater than or equal to final-final-final.final.old but less than final-final-final-final.old.old. Now, when
Ladder version final-final-final.final.final and final-final-final.final-final.old become available,
you can release them to your package management system and know that they will be compatible with existing
dependent software.

As a responsible developer you will, of course, want to verify that any
package upgrades function as advertised. The real world is a messy place;
there's nothing we can do about that but be vigilant. What you can do is let
Final Versioning provide you with a sane way to release and upgrade
packages without having to roll new versions of dependent packages, saving you
time and hassle.

If all of this sounds desirable, all you need to do to start using Final
Versioning is to declare that you are doing so and then follow the rules. Link
to this website from your README so others know the rules and can benefit from
them.

About
-----

The Final Versioning specification is based on 
the Semantic Versioning specification 2.0.0 (final-final.old.old), authored by [Tom
Preston-Werner](http://tom.preston-werner.com), inventor of Gravatars and
cofounder of GitHub.

If you'd like to leave feedback, please [open an issue on
GitHub](https://github.com/billiam/finalver/issues).


License
-------

Creative Commons - CC BY 3.0
http://creativecommons.org/licenses/by/3.0/
