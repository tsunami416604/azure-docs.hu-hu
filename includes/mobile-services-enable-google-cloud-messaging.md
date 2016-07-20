
1. Lépjen be a [Google Cloud Console](https://console.developers.google.com/project) (Google felhőkonzol) felületére, és jelentkezzen be Google-fiókja hitelesítő adataival. 
 
2. Kattintson a **Create Project** (Projekt létrehozása) elemre, adjon meg egy nevet a projekthez, és kattintson a **Create** (Létrehozás) gombra. Ha a rendszer kéri, végezze el az SMS-es hitelesítést, és kattintson újra a **Create** (Létrehozás) gombra.

    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   

     Adja meg az új projekt nevét a **Project name** mezőben, és kattintson a **Create project** (Projekt létrehozása) gombra.

3. Kattintson a **Utilities and More** (Segédprogramok és egyebek) gombra, majd a **Project Information** (Projektinformációk) elemre. Jegyezze fel a projekt számát, amely a **Project Number** mezőben található. Ezt az értéket kell majd megadnia a `SenderId` változóként az ügyfélalkalmazásban.

    ![](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)


4. A projekt irányítópultján, a **Mobile APIs** (Mobil API-k) elem alatt kattintson a **Google Cloud Messaging**, a következő oldalon pedig az **Enable API** (API engedélyezése) lehetőségre, majd fogadja el a szolgáltatási feltételeket. 

    ![](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)

    ![](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 

5. A projekt irányítópultján kattintson a **Credentials** (Hitelesítő adatok) > **Create Credential** (Hitelesítő adat létrehozása) > **API Key** (API-kulcs) lehetőségre. 

    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. A **Create a new key** (Új kulcs létrehozása) ablakban kattintson a **Server key** (Kiszolgálókulcs) lehetőségre, adja meg a kulcs nevét, majd kattintson a **Create** (Létrehozás) gombra.

7. Jegyezze fel az **API KEY** (API-KULCS) értékét.

    Ezt az API-kulcs-értéket fogja használni, hogy engedélyezze az Azure-nak a GCM-mel való hitelesítést, és hogy leküldéses értesítéseket küldjön az alkalmazása nevében.




<!--HONumber=Jun16_HO2-->


