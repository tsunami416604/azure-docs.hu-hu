
###Google Cloud Messaging-projekt létrehozása API-kulcs segítségével

>[AZURE.NOTE] Az eljárás végrehajtásához egy hitelesített e-mail-címmel rendelkező Google-fiókra lesz szükség. Új Google-fiók létrehozásához látogassa meg az <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> webhelyet.

1. Lépjen a [Google Cloud Console](https://console.developers.google.com/project)-ra, és jelentkezzen be Google-fiókja hitelesítő adataival.

2. Kattintson az **All projects** (Minden projekt), majd a **Create Project** (Projekt létrehozása) gombra.

3. Adja meg a **projekt nevét**, majd kattintson a **Create** (Létrehozás) gombra.

4. Miután a projekt létrejött, mindenképpen jegyezze fel a **projekt számát**, amely egy hosszú numerikus érték. Ezt az **IAM & Admin** (IAM és rendszergazda) szakaszban találja meg a projektre vonatkozó **Settings** (Beállítások) között, és később még szüksége lesz rá. 
 
    ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. Most létrehoznunk egy kulcsot a Google Cloud Messaging platformhoz, amelyet a platformunk arra fog használni, hogy értesítéseket küldjön az Android rendszerű eszközökre. Lépjen az **API Manager** (API-kezelő) szakaszhoz, és kattintson a **Google Cloud Messaging** elemre a **Mobile APIs** (Mobil API-k) alatt. 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. A következő lapon kattintson az **Enable** (Engedélyezés) gombra. Az irányítópult felkéri, hogy hozzon létre hitelesítő adatokat. Kattintson a **Go to Credentials** (Ugrás a hitelesítő adatokhoz) gombra. 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. Válassza a **Google Cloud Messaging** elemet az első, és a **Web server** (Webkiszolgáló) elemet a második legördülő menüből. Végül kattintson a **What credentials do I need?** (Milyen hitelesítő adatokra van szükségem?) elemre.

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. Az **Add credentials to your project** (Hitelesítő adatok hozzáadása a projekthez) lapon kattintson a **Create API key** (API-kulcs létrehozása) gombra.

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. Jegyezze fel az **API KEY** (API-KULCS) értékét. A későbbiekben ezzel az API-kulccsal konfigurálhatja a „Natív leküldés” (Native Push) szakaszt. Kattintson a **Done** (Kész) gombra.



<!--HONumber=sep16_HO1-->


