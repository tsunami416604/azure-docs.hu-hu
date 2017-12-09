
## <a name="create-an-application-express"></a>Hozzon létre egy alkalmazást (Express)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:
1. Az alkalmazás regisztrálása a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)
2.  Adjon meg egy nevet az alkalmazás és az e-maileket
3.  Győződjön meg arról, hogy az interaktív telepítés beállítást
4.  Az utasítások beszerzéséhez, és illessze be a kódot

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Az alkalmazás regisztrációs adatokat hozzáadni a megoldás (speciális)

1.  Ugrás a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app)
2.  Adjon meg egy nevet az alkalmazás és az e-maileket
3.  Győződjön meg arról, hogy az interaktív telepítés beállítás nincs bejelölve
4.  Kattintson a `Add Platform`, majd jelölje be `Native Application` kattintson`Save`
5.  Térjen vissza az xcode-ban. A `ViewController.swift`, cserélje le a kezdetű sort "`let kClientID`" az imént regisztrált alkalmazás azonosítójával:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
CTRL + kattintson <code>Info.plist</code> a helyi menü, és kattintson a: <code>Open As</code>> <code>Source Code</code>
</li>
<li>
Az a <code>dict</code> gyökércsomópont, adja hozzá a következő:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Cserélje le <i> <code>[Your_Application_Id_Here]</code> </i> az imént regisztrált alkalmazás azonosítójával
</li>
</ol>
