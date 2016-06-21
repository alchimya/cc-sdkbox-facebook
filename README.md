# cc-sdkbox-facebook
A Cocos Creator Project with SDKBOX Facebook (iOS tested, very soon Android too)

# Using SKBOX Facebook Plugin with a Cocos Creator Project (iOS Version)

This solution has been tested with an iOS Project, but very soon will be tested with an Android project too.
<br/>
Here all the steps that you need to setup the SDKBOX Facebook Plugin using sdkbox installer command line tool.
<br/>

- Get the SDK Box installer following the instruction here http://docs.sdkbox.com/en/installer
- Open your Cocos Creator Project and build for iOS
- Create an empty file <b>project.properties</b> in to <b>/build/jsb-default/frameworks/runtime-src/proj.android-studio/app</b>
- Open the terminal and execute the command <b>cd MY_PROJECT_FOLDER/build/jsb-default</b>
- execute the command <b>sudo sdkbox import facebook</b>. Probably you will get the error "failed to patch file AppDelegate.cpp"....Don't worry :)
- open the folder <b>/build/jsb-default/frameworks/runtime-src/Classes</b> and set, for all the plugin classes, a <b>Read & Write</b> permission for <b>everyone</b>
- Open the file /build/jsb-default/frameworks/cocos2d-x/cocos/cocos2d.h and replace 
```objectivec
#define COCOS2D_VERSION 0x00030901
```
with
```objectivec
#define COCOS2D_VERSION 0x00031000
```
- Open the file <b>/build/jsb-default/frameworks/runtime-src/Classes/AppDelegate.cpp</b> and add:
```objectivec
	#include "PluginFacebookJS.hpp"
	#include "PluginFacebookJSHelper.h"
	#include "PluginFacebookJS.hpp"
	#include “PluginFacebookJSHelper.h"
```
and into the <b>bool AppDelegate::applicationDidFinishLaunching()</b> method before the instruction <b>sc->start();</b> add
```objectivec
sc->addRegisterCallback(register_all_PluginFacebookJS);
sc->addRegisterCallback(register_all_PluginFacebookJS_helper);
```
- Open the file <b/>build/jsb-default/frameworks/runtime-src/Classes/PluginFacebookJSHelper.cpp</b> and replace this method
```objectivec
bool js_PluginFacebookJS_PluginFacebook_getFriends(JSContext *cx, uint32_t argc, jsval *vp)
{
    JS::CallArgs args = JS::CallArgsFromVp(argc, vp);
    if (argc == 0) {
        std::vector<sdkbox::FBGraphUser> ret = sdkbox::PluginFacebook::getFriends();
        size_t size = ret.size();
        
#ifndef __COCOS2D_CCDEPRECATED_H__
        cocos2d::ValueVector array;
        for (int i = 0; i < size; i++)
        {
            const sdkbox::FBGraphUser& friendInfo = ret.at(i);
            cocos2d::ValueMap friendInfoDict;
            friendInfoDict[FBGraphUser_ID] = friendInfo.uid;
            friendInfoDict[FBGraphUser_NAME] = friendInfo.name;
            friendInfoDict[FBGraphUser_FIRST_NAME] = friendInfo.firstName;
            friendInfoDict[FBGraphUser_LAST_NAME] = friendInfo.lastName;
            friendInfoDict[FBGraphUser_INSTALLED] = friendInfo.isInstalled;
            array.push_back(cocos2d::Value(friendInfoDict));
        }
        jsval jsret = ccvaluevector_to_jsval(cx, array);
#else
        cocos2d::CCArray *array = cocos2d::CCArray::create();
        array->retain();
        for (int i = 0; i < size; i++)
        {
            const sdkbox::FBGraphUser& friendInfo = ret.at(i);
            cocos2d::CCDictionary *friendInfoDict = cocos2d::CCDictionary::create();
            friendInfoDict->setObject(cocos2d::CCString::create(friendInfo.uid), FBGraphUser_ID);
            friendInfoDict->setObject(cocos2d::CCString::create(friendInfo.name), FBGraphUser_NAME);
            friendInfoDict->setObject(cocos2d::CCString::create(friendInfo.firstName), FBGraphUser_FIRST_NAME);
            friendInfoDict->setObject(cocos2d::CCString::create(friendInfo.lastName), FBGraphUser_LAST_NAME);
            friendInfoDict->setObject(cocos2d::CCBool::create(friendInfo.isInstalled), FBGraphUser_INSTALLED);
            
            array->addObject(friendInfoDict);
        }
        
        jsval jsret = ccarray_to_jsval(cx, array);
#endif // __COCOS2D_CCDEPRECATED_H__
        args.rval().set(jsret);
        return true;
    }
    JS_ReportError(cx, "js_PluginFacebookJS_PluginFacebook_getFriends : wrong number of arguments");
    return false;
}
```
- If doesn’t already exist, add the <b>file /build/jsb-default/res/sdkbox_config.json plugin</b>
<br/>You can get it form the official GitHub idk box Facebook sample here:
<br/>
https://github.com/sdkbox/sdkbox-sample-facebook/blob/master/js/res/sdkbox_config.json
<br/>
Anyway it has the following content:
```javascript
{
    "android": {
        "Facebook": {
            "debug": false
        }
    }, 
    "ios": {
        "Facebook": {
            "debug": true
        }
    }
}
```

- Open the Xcode project (<b/>/build/jsb-default/frameworks/runtime-src/proj.ios_mac</b>) and execute the steps regarding <b/>AppController.mm</b> and <b>.plist</b> file 
as reported here:
<b/>
http://docs.sdkbox.com/en/plugins/facebook/v2-js

# References
http://discuss.cocos2d-x.org/t/sdkbox-unfortunately-again/30229
<br/>
http://discuss.cocos2d-x.org/t/sdkbox-plug-in-for-cocos-creator/30205

<br/>
Special thanks to:
<br>
 <a href="http://discuss.cocos2d-x.org/users/yinjimmy/">yinjimmy</a>



