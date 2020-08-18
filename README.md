# SCCocoaPods

Standard Cyborg's private Cocoa Pods registry.

Note: if you are unfamiliar with CocoaPods, this repository is a CocoaPods "registry" that serves
to provide a versioned record of CocoaPods specs.  This repository does _not_ serve to house the
development of those specs themselves; you probably want a separate repo for developing your
spec or project.  FMI see notes below as well as these projects, which are simple CocoaPods 
wrappers around existing open source C/C++ projects:
 * https://github.com/StandardCyborg/LibArchiveCocoa
 * https://github.com/StandardCyborg/FMTCocoa
 * https://github.com/StandardCyborg/GTestCppCocoa
 * https://github.com/StandardCyborg/EigenCPPCocoa


## Example Use

Set up the repo:
`$ pod repo add SCCocoaPods git@github.com:StandardCyborg/CocoaPods.git`

Push your spec to the repo:
`$ pod repo push SCCocoaPods MyLib.podspec.json`

Use your spec in another project by adding these lines to the top
if your project's `Podfile`:
```
source 'https://github.com/CocoaPods/Specs.git'
source 'git@github.com:StandardCyborg/CocoaPods.git'
```


## Adding a Pod: C++

Let's suppose you have some C/C++ code that you'd like to wrap
in a Cocoa Pod.  

### Create a Podspec; Test it Locally

First, create a [Podspec](https://guides.cocoapods.org/syntax/podspec.html)
file.  The [Protobuf C++](https://github.com/protocolbuffers/protobuf/blob/02f182e829c7ae51eae0b9a24a231b8e6473f3ff/Protobuf-C%2B%2B.podspec#L1)
podspec is a good example, and you can find others in this repo.

Next, use your Podspec file in your app's Podfile; FMI see
the [official tutorial](https://guides.cocoapods.org/using/using-cocoapods.html).
You probably just want to reference your Podspec by path, i.e. your
Podfile should have something like:
```
target 'MyApp' do
  pod 'MyLib', :podspec => 'path/to/MyLib.podspec'
end
```

#### Cocoa Madness: Developing Multiple Specs at a Time

Suppose you're creating two or more Podspecs at once; let's call
them `LibA` and `LibB`.  Further suppose `LibA` requires `LibB`
as a dependency.  (For example, `LibA` might be a custom library
that uses Protobuf, and `LibB` might be your own custom Podspec
for `Protobuf`).  

Note that sadly you can't have Podspecs reference other Podspecs by path;
in particular, the `dependencies` section of Podspec can't contain the
special `:podspec` or `:path` keys that Podfiles can use.  (At the time of
writing, if you use these special keys in a Podspec, you'll get an exception
from the Ruby impl of CocoaPods).  

Thus, in the Podspec for `LibA`, you must express the dependency as
`dependencies: ['LibB', '~> 1.0']`, even if `LibB` does not (yet) exist
in any Podspec repo (like this repo).

But! In your app's Podfile, you can reference both by path, and CocoaPods
will resolve the `LibA` <- `LibB` dependency using your local filesystem;
i.e. this should work:

```
target 'MyApp' do
  pod 'LibA', :podspec => 'path/to/LibA.podspec'
  pod 'LibB', :podspec => 'path/to/LibB.podspec'  # LibA requires LibB
end
```

### Pushing Your Podspec

Once your Podspec works, you can push it to this repo as note above
using:
`$ pod repo push SCCocoaPods MyLib.podspec.json`

Note that the `pod` client will attempt to build your Podspec code
before pushing it; furthermore, some C++ projects can have collisions
with others.  You might want to use:

`$ pod repo push SCCocoaPods MyLib.podspec.json --verbose --allow-warnings --use-libraries --use-modular-headers`

