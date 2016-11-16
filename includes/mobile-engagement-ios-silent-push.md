> [!IMPORTANT]
> Ahhoz, hogy leküldéses értesítéseket fogadjon a Mobile Engagement programtól, engedélyeznie kell a `Silent Remote Notifications` elemet az alkalmazásban. Az Info.plist fájlban hozzá kell adnia a távértesítési értéket az UIBackgroundModes tömbhöz.
> 
> 

1. Nyissa meg az `info.plist` fájlt a projektben
2. Kattintson a jobb egérgombbal a lista legfelső elemére (`Information Property List`), és adjon hozzá egy új sort
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)
3. Adja meg a következőt az új sorban `Required background modes`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)
4. Kattintson a balra mutató nyílra a sor kibontásához
5. Adja a következő értéket a 0. elemhez `App downloads content in response to push notifications`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)
6. A módosítás elvégzése után az info.plist XML-fájlnak a következő kulcsot és értéket kell tartalmaznia:
   
        <key>UIBackgroundModes</key>
        <array>
        <string>remote-notification</string>
        </array>
7. **Xcode 7+** és **iOS 9+** használata esetén:
   
   * Engedélyezze a **Push Notifications** (leküldéses értesítések) beállítást a Targets (Célok) > Your Target Name (Saját cél neve) > Capabilities (Képességek) menüben.



<!--HONumber=Nov16_HO2-->


