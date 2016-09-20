###A GCM API-kulcshoz való hozzáférés biztosítása a Mobile Engagement számára

Ha engedélyezni szeretné, hogy a Mobile Engagement leküldéses értesítéseket küldjön az Ön nevében, hozzáférést kell biztosítania számára az API-kulcshoz. Ezt a kulcs konfigurálásával, valamint annak a Mobile Engagement portálon történő megadásával teheti meg.

1. A klasszikus Azure portálon győződjön meg arról, hogy be van lépve a projekthez használt alkalmazásba, majd kattintson a lap alján található **Engage** (Aktiválás) gombra:

    ![](./media/mobile-engagement-android-send-push/engage-button.png)

2. Ezután kattintson a **Beállítások** -> **Natív leküldés** elemre a GCM-kulcs megadásához:

    ![](./media/mobile-engagement-android-send-push/engagement-portal.png)

3. Kattintson az **API-kulcs** melletti **Szerkesztés** ikonra a **GCM-beállítások** szakaszban, ahogy az ábra is mutatja:

    ![](./media/mobile-engagement-android-send-push/native-push-settings.png)

4. A felugró ablakban illessze be az előzőleg beszerzett GCM-kiszolgálókulcsot, majd kattintson az **OK** gombra.

    ![](./media/mobile-engagement-android-send-push/api-key.png)

##<a id="send"></a>Értesítés küldése az alkalmazásnak

Ezután létrehozunk egy egyszerű leküldéses értesítési kampányt, amely elküld egy leküldéses értesítést az alkalmazásnak.

1. Lépjen a Mobile Engagement portál **REACH** (ELÉRÉS) lapjára.

2. Kattintson a **New announcement** (Új értesítés) elemre a leküldéses értesítési kampány létrehozásához.

    ![](./media/mobile-engagement-android-send-push/new-announcement.png)

3. Állítsa be a kampány első mezőjét a következő lépésekkel:

    ![](./media/mobile-engagement-android-send-push/campaign-first-params.png)

    a. Nevezze el a kampányt.

    b. A Kézbesítési típust (**Delivery type**) állítsa *System notification -> Simple* (Rendszerértesítés -> Egyszerű) lehetőségre. Ez egy egyszerű Android leküldéses értesítési típus, amely egy címet és egy rövid szöveges sort tartalmaz.

    c. A **Delivey time** (Kézbesítés időpontja) beállítást állítsa *Any time* (Bármikor) értékre. Így az alkalmazás akkor is megkapja az értesítéseket, ha nincs elindítva.

    d. Az értesítés szövegéhez adja meg a címet (**Title**), amely a leküldéses üzenetben félkövérrel szedve jelenik majd meg.

    e. Ezután írja be az üzenetét a **Message** (Üzenet) mezőbe

4. Görgessen lefelé, és a **Content** (Tartalom) szakaszban válassza a **Notification only** (Csak értesítés) lehetőséget.

    ![](./media/mobile-engagement-android-send-push/campaign-content.png)

5. Ezzel beállította a lehető legegyszerűbb kampányt. Görgessen újra le, és kattintson a **Create** (Létrehozás) gombra a kampány mentéséhez.

6. Utolsó lépés: Kattintson az **Activate** (Aktiválás) lehetőségre a kampány aktiválásához és a leküldéses értesítések küldésének megkezdéséhez.

    ![](./media/mobile-engagement-android-send-push/campaign-activate.png)


<!--HONumber=sep16_HO1-->


