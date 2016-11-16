### <a name="grant-access-to-your-push-certificate-to-mobile-engagement"></a>A leküldéses tanúsítványhoz való hozzáférés biztosítása a Mobile Engagement számára
Ha engedélyezni szeretné, hogy a Mobile Engagement leküldéses értesítéseket küldjön az Ön nevében, hozzáférést kell biztosítania számára a tanúsítványhoz. Ezt a tanúsítvány konfigurálásával, valamint annak a Mobile Engagement portálon történő megadásával teheti meg. Győződjön meg róla, hogy az [Apple dokumentációjában](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6) leírtak alapján szerezte be .p12-tanúsítványát.

1. Lépjen a Mobile Engagement portálra. Győződjön meg arról, hogy a megfelelő helyen van, majd kattintson a lap alján található **Engage** (Aktiválás) gombra:
   
    ![](./media/mobile-engagement-ios-send-push/engage-button.png)
2. Kattintson a **Settings** (Beállítások) lapra az Engagement portálon. Itt kattintson a **Native Push** (Natív leküldés) szakaszra a p12-tanúsítvány feltöltéséhez:
   
    ![](./media/mobile-engagement-ios-send-push/engagement-portal.png)
3. Válassza ki a p12-tanúsítványát, töltse fel, majd írja be a jelszavát:
   
    ![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

## <a name="a-idsendasend-a-notification-to-your-app"></a><a id="send"></a>Értesítés küldése az alkalmazásnak
Most létrehozunk egy egyszerű leküldéses értesítési kampányt, amely elküld egy leküldéses értesítést az alkalmazásnak.

1. Lépjen a Mobile Engagement portál **Reach** (Elérés) lapjára.
2. Kattintson a **New Announcement** (Új értesítés) elemre a leküldéses kampány létrehozásához.
   
    ![](./media/mobile-engagement-ios-send-push/new-announcement.png)
3. Adja meg a kampány első mezőinek értékét:
   
    ![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)
   
   * A **Name** (Név) mezőben adja meg a kampány nevét. 
   * A **Delivey time** (Kézbesítés időpontja) beállítást állítsa **Out of app only** (Csak alkalmazáson kívül) értékre. Ez egy egyszerű Apple leküldéses értesítési típus, amely egy kevés szöveget tartalmaz.
   * Az értesítés szövegéhez először adja meg a címet (**Title**), amely a leküldéses üzenetben az elsős sorban jelenik majd meg.
   * Ezután írja be az üzenetét (**Message**), amely a második sorban jelenik majd meg.
4. Görgessen lefelé, és a tartalom szakaszban válassza a **Notification only** (Csak értesítés) lehetőséget.
   
    ![](./media/mobile-engagement-ios-send-push/campaign-content.png)
5. Ezzel beállította a lehető legegyszerűbb kampányt. Görgessen lefelé, és kattintson a **Create** (Létrehozás) gombra a leküldéses értesítési kampány mentéséhez. 
6. Végezetül kattintson az **Activate** (Aktiválás) gombra a leküldéses értesítés kiküldéséhez. 
   
    ![](./media/mobile-engagement-ios-send-push/campaign-activate.png)
7. Az értesítést az alábbi formában fogadhatja iOS-eszközének értesítési központjában:
   
    ![](./media/mobile-engagement-ios-send-push/iphone-notification.png)
8. Ha rendelkezik egy, az iOS-eszközzel párosított Apple Watchcsal, akkor az értesítés az óráján jelenik majd meg.
   
    ![](./media/mobile-engagement-ios-send-push/apple-watch.png)



<!--HONumber=Nov16_HO2-->


