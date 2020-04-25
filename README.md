# CocoaPods
Private CocoaPods registry for internal use

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


