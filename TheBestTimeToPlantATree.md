
## Logging
 [Online Article](https://dev.epicgames.com/documentation/en-us/unreal-engine/logging-in-unreal-engine)
### basic logs & the TEXT() macro
arguments for TEXT macro are formatted as ``TEXT("message %<X> "), arg1`` where ``<X>`` is the argument type used by unreal's text processing system
text macro also puts extra params AFTER init close paren

| Type    | Argument | Extra/notes                                                      |
| ------- | -------- | ---------------------------------------------------------------- |
| FString | \%s      | must derefence ``*ExampleActor->GetName()``                      |
| bool    | %s       | ternary logic:``( bExampleBool ? TEXT("true"): TEXT("false") )`` |
| int     | %d       | Doesn't work with Doubles!!!                                     |
| double  | %f       |                                                                  |
| float   | %f       |                                                                  |
| FVector | %s       | ToString() call, must be deref'd ``*ExampleVector.ToString()``   |
| FText   | %s       | needs .ToString() call                                           |
example of a full command would be:
```cpp
UE_LOG(LogTemp, Warning, TEXT("Current values are: vector %s, float %f, and integer %d"), *ExampleVector.ToString(), ExampleFloat, ExampleInteger);
```
### structured logs
5.2 and later has [UE_LOGFMT](https://unrealcommunity.wiki/logging-lgpidy6i#logging-using-ue_logfmt)
```cpp
 #include "Logging/StructuredLog.h"``

UE_LOGFMT(LogTemp, Log, "Message"); //note that it DOES have a ; at the end
```

two options for parameters:
```cpp

//positional:
UE_LOGFMT(LogCore, Warning, "Loading '{0}' failed with error {1}", Package-&gt;GetName(), ErrorCode);

//and named
UE_LOGFMT(LogCore, Warning, "Loading '{Name}' failed with error {Error}", ("Error", ErrorCode), ("Name", Package-&gt;GetName()), ("Flags", LoadFlags));
```

make sure you get the parameters right (i.e. don't pass the func and arg and forget to give it a slot) otherwise it'll crash

# custom categories

add the following macro to your projectName.h file
```cpp
DECLARE_LOG_CATEGORY_EXTERN(BSSEvents, Log, All);
```

and the following macro to your projectName.cpp file
```cpp
DEFINE_LOG_CATEGORY(BSSEvents);
```
failure to include both will prompt [[Error index#Unresolved external|unresolved external]] error


[read more](https://nerivec.github.io/old-ue4-wiki/pages/logs-printing-messages-to-yourself-during-runtime.html#Setting_Up_Your_Own_Log_Category)

# Formatting Text otherwise
for when you need to pass arguments into a text that you aren't printing to the log
FText::format() is the argument, full syntax is as follows:
```    c++
FText FlavorTextToShow = FText::Format( NSLOCTEXT("LocalNameSpace", "ExampleFText", "Param 1 is {0} "), FText::AsNumber(5)));

```
the LocalNameSpace var is largely for your end of things - keeping track of where the text is originating. Generally recommended to have it be named after your class/file name. Module name may also work
unreal's docs also used the following example:

```c++
FText::Format(LOCTEXT("ExampleFText", "You currently have {0} health left."), 5);
```
which relocates the definition of the LocNameSpace variable, but requires you to include the following definitions at the start and end of your file:
```c++
#define LOCTEXT_NAMESPACE "YourNamespace"
//^ at the start 

#undef LOCTEXT_NAMESPACE
//^ at the end 

```
the following include may also be needed, but I don't esp think so:

``` c++
#include "Internationalization/Text.h"
```

