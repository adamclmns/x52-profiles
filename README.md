# x52-profiles
The X52 Profiles I have collected and use. 


Things I've learned about the .pr0 files by 'just messing around'

All profiles will follow a predictable structure,

```
[profile= version = 
    [controllers   
        [controller=... group...
            [controls ...]
            [shifts ...]    
        ]
    ]
    [commands ...]
]
```

The Controls section is where some initial 'hacks' can be found. 

Example: The MFD buttons on the X52 show up in the joy.cpl test page as buttons 27, 28, 29. These buttons are not mappable in the profiler, and they are not configurable in most games if you have any *standard* profile loaded. 

Adding the following to the `[controls ...]` section for the X52, will allow these buttons to be configured in games. 
```
        [button=0x0009001B name=27]
        [button=0x0009001C name=28]
        [button=0x0009001D name=29]
```
To assign an action to these buttons, you can create the action in a fresh profile with the profile first. Then open that file and locate your action by name.   Here's an example '[actioncommand ...]` node to give you an idea of what you're looking for
```
 [actioncommand=d96ea4f3-2158-4c95-88e8-9c8bc71c99f4 name='Simulator - Kneeboard (display/hide)'
      [actionblock=macro
        [action device=keyboard usage=0x000000E1 page=0x00000007 value=0x00000001]
        [action device=keyboard time=0x00000195 usage=0x00000043 page=0x00000007 value=0x00000001]
        [action device=keyboard time=0x0000006D usage=0x00000043 page=0x00000007]
        [action device=keyboard time=0x000000EA usage=0x000000E1 page=0x00000007]]
      [actionblock type=repeat]
      [actionblock=macro type=release]]

```

You'll also need to find the `[shift [assignments [button]]]` node and create the same stucture in your profile. Use the "ActionCommand" UUID to find the corresponding button assingment(s) you want. Note the `command=` sharing the same UUID as our ActionCommand. Reproduce the `[shift [ assignments ]]` structure and add your button, or just insert it in an already established structure.

```
[button=0x0009000E role=bands
              [bands
                [band]
                [band=1 command=d96ea4f3-2158-4c95-88e8-9c8bc71c99f4]]]
```

Now, you just need to change the `button` hexadecimal assignment. 

Note: opening and saving a profile in the editor will remove any 'invalid' assignments, so once you've made hand-edits, be very careful. This is why I have all of mine in git. 

Other things of Note:


Peeking inside the ST.exe's accompanying DLL's, I've been able to glean and infer a lot of information quickly. 

`role=`
----
Enum value. Can be `bands`,`fallback`,`increment`,`mouse`,`reportas`,`unprogrammed`

`controls` contains a list of `control` objects, which can be of type, `axis`,`button`,`hat`,`mouse-axis`,`mouse-pointer`,`programmable` (? Guessing based off of TypeNames...)



an `actioncommand` has an ID, a name that prints on the MFD when pressed, also contains an `actionblock` which can be as simple as 
```
[actionblock
        [action device=mouse usage=0x00000038 page=0x00000001 value=0x00000001]]
```
but can also include more options for more complicated scenarios
```
[actioncommand=4fd2d181-7341-42dd-93f2-a87b9a6533a6 name='Radio - VOR 2 Ident (on/off)'
      [actionblock=macro
        [action device=keyboard usage=0x000000E0 page=0x00000007 value=0x00000001]
        [action device=keyboard time=0x00000140 usage=0x0000001F page=0x00000007 value=0x00000001]
        [action device=keyboard time=0x00000064 usage=0x0000001F page=0x00000007]
        [action device=keyboard time=0x000000BE usage=0x000000E0 page=0x00000007]]
      [actionblock type=repeat]
      [actionblock=macro type=release]]
```

