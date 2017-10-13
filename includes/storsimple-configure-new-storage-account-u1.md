<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-add-a-storage-account-in-storsimple-8000-series-update-10"></a>Tárfiók hozzáadása a StorSimple 8000 Series 1.0-s frissítésében
1. A StorSimple Manager szolgáltatás kezdőlapján válasza ki a szolgáltatást, és kattintson rá duplán. Ekkor a **Gyors üzembe helyezés** oldalra kerül. Válassza a **Konfigurálás** lapot.
2. Kattintson a **Tárfiók hozzáadása/szerkesztése** elemre.
3. A **Tárfiók hozzáadása/szerkesztése** párbeszédpanelen kattintson az **Új hozzáadása** parancsra.
4. A **Szolgáltató** mezőben válassza ki a kívánt felhőszolgáltatót. A támogatott szolgáltatók a következők: Azure, Amazon S3, Amazon S3 with RRS, HP és OpenStack. Adja meg a felhőszolgáltatók tárfiókjához tartozó hitelesítő adatokat és helyet. A hitelesítő adatok megadására szolgáló mezők a megadott felhőszolgáltatóktól függően eltérhetnek. 
   
   * Ha az Azure felhőszolgáltatást választotta, adja meg a Microsoft Azure-tárfiókhoz tartozó **nevet** és az elsődleges **elérési kulcsot**. Azure-fiók esetén a hely mező automatikusan ki lesz töltve.
     
        ![Azure-tárfiók hozzáadása](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)
   * Ha az Amazon S3 vagy az Amazon S3 with RRS szolgáltatót választotta, adjon meg egy rövid **tárfióknevet**, **elérési kulcsot** és **titkos kulcsot**. Az Amazon S3 és az Amazon S3 with RRS esetén az alábbi helyek támogatottak:
     
     * USA szabvány
     * USA nyugati régiója (Oregon)
     * USA nyugati régiója (Észak-Kalifornia)
     * EU (Írország)
     * Ázsia és a Csendes-óceáni térség (Szingapúr)
     * Ázsia és a Csendes-óceáni térség (Sydney)
     * Ázsia és a Csendes-óceáni térség (Tokió)
     * Dél-Amerika (Sao Paulo)
       
       ![Amazon-tárfiók hozzáadása](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
   * Ha a HP felhőszolgáltatót választotta, adjon meg egy rövid **tárhelyfióknevet**, **bérlőazonosítót**, **felhasználónevet** és **jelszót**. A HP esetén az alábbi helyek támogatottak:
     
     * USA keleti régiója
     * USA nyugati régiója
       
       ![HP-tárfiók hozzáadása](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
   * Ha az **Openstack** felhőszolgáltatót választotta, adjon meg egy **állomásnevet**, **elérési kulcsot** és **titkos kulcsot**.
     
     > [!NOTE]
     > Az Azure kivételével minden felhőszolgáltató esetén egy rövid nevet is megadhat. Ugyanazon hitelesítő adatokkal, de több különböző rövid névvel több tárfiókot is létrehozhat.
     > 
     > 
     
        ![Openstack-tárfiók hozzáadása](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)
5. Válassza az **SSL-mód engedélyezése** lehetőséget, ha egy biztonságos csatornát szeretne létrehozni az eszköz és a felhő közötti hálózati kommunikációhoz. Csak akkor törölje a jelölést az **SSL-mód engedélyezése** jelölőnégyzetből, ha magánfelhőben tevékenykedik.
   
   > [!NOTE]
   > Ha a HP szolgáltatót választotta, akkor az SSL-mód mindig engedélyezve van.
   > 
   > 
6. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png). Értesítést kap, ha a tárfiók sikeresen létrejött.
7. Az újonnan létrehozott tárfiók megjelenik a **Tárfiókok** terület **Konfigurálás** lapján. Az új tárfiók mentéséhez kattintson a **Mentés** gombra. Ha a rendszer megerősítést kér, kattintson az **OK** gombra.

