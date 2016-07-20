<properties
   pageTitle="Azure AD Connect: Frissítés a DirSync szolgáltatásról | Microsoft Azure"
   description="Tudja meg, hogyan frissíthet a DirSync szolgáltatásról az Azure AD Connect szolgáltatásra. Ez a cikk a DirSync szolgáltatásról az Azure AD Connect szolgáltatásra való frissítés lépéseit ismerteti."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="05/31/2016"
   ms.author="andkjell;shoatman;billmath"/>

# Azure AD Connect: Frissítés a DirSync szolgáltatásról
Az Azure AD Connect a DirSync utóda. A DirSync szolgáltatásról való frissítés lehetőségeit találja meg ebben a témakörben. Ezek lépések nem használhatóak az Azure AD Connect másik kiadásáról vagy az Azure AD Syncről való frissítéshez.

Mielőtt elkezdené telepíteni az Azure AD Connect szolgáltatást, bizonyosodjon meg róla, hogy [letöltötte az Azure AD Connectet](http://go.microsoft.com/fwlink/?LinkId=615771), és elvégezte az [Azure AD Connect: Hardware and prerequisites](active-directory-aadconnect-prerequisites.md) (Azure AD Connect: hardver és előfeltételek) témakörben leírt lépéseket.

Ha nem a DirSync szolgáltatásról frissít, tekintse meg az egyéb forgatókönyvek [vonatkozó dokumentációját](#related-documentation).

## Frissítés a DirSync szolgáltatásról
A DirSync jelenleg aktív üzemelő példányától függően több lehetőség is létezik a frissítésre. Ha a várt frissítési idő kevesebb mint 3 óra, akkor a helyben történő frissítés javasolt. Ha a várt frissítési idő több mint 3 óra, akkor a párhuzamos üzembe helyezés javasolt egy másik kiszolgálón. A becslések szerint 50 000-nél több objektum esetén a frissítés több mint 3 órát vesz majd igénybe.

Forgatókönyv |  
---- | ----
[Frissítés helyben](#in-place-upgrade)  | Ez az előnyben részesített lehetőség, ha a frissítés várhatóan 3 óránál kevesebb időt vesz igénybe.
[Párhuzamos üzembe helyezés](#parallel-deployment) | Ez az előnyben részesített lehetőség, ha a frissítés várhatóan 3 óránál több időt vesz igénybe.

>[AZURE.NOTE] Amikor a DirSync szolgáltatásról az Azure AD Connect szolgáltatásra frissít, ne telepítse a DirSyncet a frissítés előtt. Az Azure AD Connect beolvassa és áttelepíti a konfigurációt a DirSyncről, és eltávolítja azt a kiszolgáló vizsgálatát követően.

**Frissítés helyben**  
A varázsló mutatja a frissítés befejeztének várható idejét. Ez a becslés azon a feltételezésen alapul, hogy egy 50 000 objektumot (felhasználókat, névjegyeket és csoportokat) tartalmazó adatbázis frissítése 3 órát vesz igénybe. Az Azure AD Connect elemzi az aktuális DirSync-beállításokat, és helyben történő frissítést javasol, ha az adatbázis 50 000-nél kevesebb objektumot tartalmaz. Ha a folytatást választja, az aktuális beállítások automatikusan alkalmazva lesznek a frissítés folyamán, és a kiszolgáló automatikusan folytatja majd az aktív szinkronizálást.

Amennyiben konfiguráció-áttelepítést és párhuzamos üzembe helyezést kíván végezni, felülbírálhatja a helyben történő frissítésre tett javaslatot. Például ez remek alkalom lehet arra, hogy frissítse a hardvert és az operációs rendszert. További információért lásd a [párhuzamos üzembe helyezés](#parallel-deployment) szakaszt.

**Párhuzamos üzembe helyezés**  
A párhuzamos üzembe helyezés alkalmazása akkor ajánlott, ha 50 000-nél több objektummal rendelkezik. Ezzel a módszerrel a felhasználók nem tapasztalnak semmilyen késedelmet a rendszer működésében. Az Azure AD Connect telepítés megkísérli megbecsülni a frissítés hátralévő idejét, azonban amennyiben már végzett korábban is DirSync-frissítést, saját tapasztalatai alapján valószínűleg jobb becsléseket adhat.

### Támogatott frissíthető DirSync-konfigurációk
A DirSync az alábbi konfigurációmódosításokat támogatja, és ezek frissítése történik meg:

- Tartományok és szervezeti egységek szűrése
- Másik azonosító (UPN)
- Jelszó-szinkronizálás és Exchange hibrid beállítások
- Erdők/tartományok és az Azure AD beállítási
- Szűrés felhasználói attribútumok alapján

Az alábbi módosítás nem frissíthető. Amennyiben végrehajtotta ezt a módosítást, a frissítés blokkolva lesz:

- Nem támogatott DirSync-módosítások, például eltávolított attribútumok, vagy egyedi bővítmény-DLL használata

![Frissítés blokkolva](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

Ezekben az esetekben az ajánlott megoldás egy új Azure AD Connect kiszolgáló telepítése [átmeneti módban](active-directory-aadconnectsync-operations.md#staging-mode), és a régi DirSync és az új Azure AD Connect konfiguráció ellenőrzése. Alkalmazza újra az összes módosítást egyéni konfiguráció használatával az [Azure AD Connect Sync custom configuration](active-directory-aadconnectsync-whatis.md) (Azure AD Connect szinkronizálása egyéni konfigurációval) szakaszban foglaltak szerint.

A DirSync által a szolgáltatásfiókokhoz használt jelszavak nem kérhetőek le, és nem lesznek áttelepítve. A jelszavak vissza lesznek állítva a frissítés során.

### A DirSync szolgáltatásról az Azure AD Connect szolgáltatásra való frissítés magas szintű lépései

1. Üdvözli az Azure AD Connect
2. Az aktuális DirSync-konfiguráció elemzése
3. Az Azure AD globális rendszergazdai jelszavának begyűjtése
4. Egy vállalati rendszergazdai fiók hitelesítő adatainak begyűjtése (csak az Azure AD Connect telepítése során való használatra)
5. Az Azure AD Connect telepítése
    * A DirSync eltávolítása
    * Az Azure AD Connect telepítése
    * Választhatóan a szinkronizálás indítása

További lépések végrehajtása szükséges, ha:

* Jelenleg teljes SQL Server rendszert használ – akár helyit, akár távolit
* 50 000-nél több objektummal rendelkezik a szinkronizálás hatókörében

## Frissítés helyben

1. Indítsa el az Azure AD Connect telepítőt (MSI).
2. Tekintse át és fogadja el a licencfeltételeket és az adatvédelmi nyilatkozatot.
![Üdvözli az Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. Kattintson a Tovább gombra a meglévő DirSync telepítés elemzésének indításához.
![A meglévő Directory Sync telepítés elemzése](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. Az elemzés végeztével a rendszer ajánlást tesz a következő lépések tekintetében.  
    - Ha SQL Server Expresst használ, és kevesebb mint 50 000 objektummal rendelkezik, a következő képernyő jelenik meg: ![Elemzés befejezve, készen áll a frissítésre a DirSyncről](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
    - Ha teljes SQL Server rendszert használ a DirSynchez, ehelyett a következő képernyőt látja majd: ![Elemzés befejezve, készen áll a frissítésre a DirSyncről](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
A DirSync által használt meglévő SQL Server adatbázis-kiszolgálóra vonatkozó információk megjelennek. Amennyiben szükséges, végezze el a megfelelő módosításokat. A telepítés folytatásához kattintson a **Next** (Tovább) gombra.
    - Ha 50 000-nél több objektummal rendelkezik, ehelyett a következő képernyőt látja majd: ![Elemzés befejezve, készen áll a frissítésre a DirSyncről](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
Amennyiben helyben történő frissítést kíván végezni, kattintson a jelölőnégyzetre az üzenet mellett: **Continue upgrading DirSync on this computer** (A DirSync frissítésének folytatása ezen a számítógépen).
Amennyiben inkább [párhuzamos üzembe helyezést](#parallel-deployment) kíván végrehajtani, exportálnia kell a DirSync konfigurációs beállításait, majd áttelepítenie azokat az új kiszolgálóra.
5. Adja meg a fiók jelszavát, amellyel jelenleg az Azure AD szolgáltatáshoz csatlakozik. Azt a fiókot kell használnia, amelyet jelenleg a DirSync használ.
![Adja meg Azure AD hitelesítő adatait](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
Ha hibaüzenetet kap és problémák adódnak a kapcsolódással, tekintse meg a [Troubleshoot connectivity problems](active-directory-aadconnect-troubleshoot-connectivity.md) (Kapcsolati problémák elhárítása) szakaszt.
6. Adjon meg egy vállalati rendszergazdai fiókot az Active Directory szolgáltatáshoz.
![Adja meg ADDS hitelesítő adatait](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Készen áll a konfigurálására. Amikor az **Upgrade** (Frissítés) gombra kattint, megtörténik a DirSync eltávolítása, az Azure AD Connect konfigurálása és a szinkronizálás indítása.
![Ready to configure (Konfigurálásra kész)](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Miután a telepítés befejeződött, jelentkezzen ki majd ismét be a Windowsba, mielőtt a Synchronization Service Managert (Szinkronizálási szolgáltatás kezelőjét) vagy a Synchronization Rule Editort (Szinkronizálási szabályok szerkesztőjét) használná, vagy bármely egyéb konfigurációmódosítást próbálna végrehajtani.

## Párhuzamos üzembe helyezés

### DirSync-konfiguráció exportálása
**Párhuzamos üzembe helyezés 50 000-nél több objektum esetén**

Ha 50 000-nél több objektummal rendelkezik, az Azure AD Connect telepítés párhuzamos üzembe helyezést javasol.

A következőhöz hasonló képernyő jelenik meg:

![Elemzés befejeződött](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Amennyiben párhuzamos üzembe helyezést kíván végrehajtani, az alábbi lépéseket kell végrehajtania:

- Kattintson az **Export settings** (Beállítások exportálása) gombra. Ha az Azure AD Connectet egy különálló kiszolgálón telepíti, ezeket a beállításokat importálhatja, hogy az aktuális DirSync összes beállítását áttelepítse az új Azure AD Connect-telepítésre.

Miután a beállítások exportálása sikeresen megtörtént, kiléphet az Azure AD Connect varázslóból a DirSync kiszolgálón. Lépjen tovább a következő lépésre [az Azure AD Connect különálló kiszolgálóra telepítéséhez](#installation-of-azure-ad-connect-on-separate-server)

**Párhuzamos üzembe helyezés 50 000-nél kevesebb objektummal**

Ha 50 000-nél kevesebb objektummal rendelkezik, azonban mégis párhuzamos üzembe helyezést kíván végrehajtani, tegye a következőket:

1. Futtassa az Azure AD Connect telepítőt (MSI).
2. Amikor megjelenik a **Welcome to Azure AD Connect** (Üdvözli az Azure AD Connect) képernyő, lépjen ki a telepítővarázslóból az „X” gombra kattintva az ablak jobb felső sarkában.
3. Nyisson meg egy parancssort.
4. Az Azure AD Connect telepítési helyéről (alapértelmezett hely: C:\Program Files\Microsoft Azure Active Directory Connect) hajtsa végre a következő parancsot:  `AzureADConnect.exe /ForceExport`.
5. Kattintson az **Export settings** (Beállítások exportálása) gombra.  Ha az Azure AD Connectet egy különálló kiszolgálón telepíti, ezeket a beállításokat importálhatja, hogy az aktuális DirSync összes beállítását áttelepítse az új Azure AD Connect-telepítésre.

![Elemzés befejeződött](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

Miután a beállítások exportálása sikeresen megtörtént, kiléphet az Azure AD Connect varázslóból a DirSync kiszolgálón. Lépjen tovább a következő lépésre [az Azure AD Connect különálló kiszolgálóra telepítéséhez](#installation-of-azure-ad-connect-on-separate-server)

### Az Azure AD Connect telepítése különálló kiszolgálóra

Ha az Azure AD Connectet egy új kiszolgálón telepíti, a rendszer feltételezi, hogy az Azure AD Connect tiszta telepítését kívánja végrehajtani. Mivel a DirSync-konfigurációt kívánja alkalmazni, néhány további lépést is végre kell hajtania:

1. Futtassa az Azure AD Connect telepítőt (MSI).
2. Amikor megjelenik a **Welcome to Azure AD Connect** (Üdvözli az Azure AD Connect) képernyő, lépjen ki a telepítővarázslóból az „X” gombra kattintva az ablak jobb felső sarkában.
3. Nyisson meg egy parancssort.
4. Az Azure AD Connect telepítési helyéről (alapértelmezett hely: C:\Program Files\Microsoft Azure Active Directory Connect) hajtsa végre a következő parancsot:  `AzureADConnect.exe /migrate`.
    Az Azure AD Connect telepítővarázsló elindul, és a következő képernyőt jeleníti meg: ![Adja meg Azure AD hitelesítő adatait](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. Válassza ki a DirSync-telepítésből exportált beállítási fájlt.
6. Konfigurálja az esetleges speciális beállításokat, beleértve a következőket:
    - Egyedi telepítési helyet adhat meg az Azure AD Connect számára.
    - Megadhatja az SQL Server egy meglévő példányát (alapértelmezett beállítás: az Azure AD Connect telepíti az SQL Server 2012 Expresst). Ne használja ugyanazt az adatbázispéldányt, mint a DirSync kiszolgáló.
    - Megadhat az SQL Serverhez való csatlakozáshoz használt szolgáltatásfiókot (ha az SQL Server adatbázis távoli, a fióknak tartományi szolgáltatásfióknak kell lennie).
Ezek a beállítások láthatóak ezen a képernyőn: ![Adja meg Azure AD hitelesítő adatait](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. Kattintson a **Next** (Tovább) gombra.
8. A **Ready to configure** (Konfigurálásra kész) oldalon hagyja a **Start the synchronization process as soon as the configuration completes** (Szinkronizálási folyamat indítása a konfiguráció befejeztével) beállítást bejelölve. A kiszolgáló [átmeneti módban](active-directory-aadconnectsync-operations.md#staging-mode) lesz, így a módosítások nem lesznek ezúttal exportálva az Azure AD szolgáltatásba.
9. Kattintson az **Install** (Telepítés) gombra.
10. Miután a telepítés befejeződött, jelentkezzen ki majd ismét be a Windowsba, mielőtt a Synchronization Service Managert (Szinkronizálási szolgáltatás kezelőjét) vagy a Synchronization Rule Editort (Szinkronizálási szabályok szerkesztőjét) használná, vagy bármely egyéb konfigurációmódosítást próbálna végrehajtani.

>[AZURE.NOTE] Megkezdődik a szinkronizálás a Windows Server Active Directory és az Azure Active Directory közt, a módosítások azonban nem lesznek exportálva az Azure AD szolgáltatásba.  Egy időben csupán egyetlen szinkronizálási eszköz exportálhat aktívan módosításokat. Ezt nevezzük [átmeneti módnak](active-directory-aadconnectsync-operations.md#staging-mode).

### Ellenőrizze, hogy az Azure AD Connect készen áll a szinkronizálás megkezdésére

Annak ellenőrzéséhez, hogy az Azure AD Connect kész átvenni a DirSync feladatait, meg kell nyitnia a **Synchronization Service Managert** (Szinkronizálási szolgáltatás kezelője) az **Azure AD Connect** csoportban a Start menüből.

Az alkalmazásban az **Operations** (Műveletek) lapot kell megtekintenie. Ezen a lapon ellenőriznie kell, hogy a következő műveletek végre lettek hajtva:

- Importálás az AD Connectoron
- Importálás az Azure AD Connectoron
- Teljes szinkronizálás az AD Connectoron
- Teljes szinkronizálás az Azure AD Connectoron

![Importálás és szinkronizálás befejezve](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

Tekintse át ezeknek a műveleteknek az eredményét, és győződjön meg róla, hogy nincsenek hibák.

Ha meg kívánja tekinteni és vizsgálni az Azure AD szolgáltatásba exportálni szándékolt módosításokat, olvassa el, hogy ellenőrizhető a konfiguráció az [átmeneti módban](active-directory-aadconnectsync-operations.md#staging-mode). Hajtsa végre a szükséges módosításokat, amíg semmi váratlant nem lát már.

Amint ez a 4 művelet befejeződik, nincsenek hibák, és elégedett az exportálni kívánt módosításokkal, kész eltávolítani a DirSyncet és engedélyezni az Azure AD Connect-szinkronizálást. Végezze el a következő két lépést az áttelepítés befejezéséhez.

### A DirSync eltávolítása (régi kiszolgáló)

- A **Programok és szolgáltatások** felületen válassza a **Windows Azure Active Directory sync tool** programot
- Távolítsa el a **Windows Azure Active Directory sync tool** eszközt
- Vegye figyelembe, hogy az eszköz eltávolítása akár 15 percet is igénybe vehet.

A DirSync eltávolítása után nem marad aktív kiszolgáló, amely exportálna az Azure AD szolgáltatásba. Végre kell hajtania a következő lépést, mielőtt folytatódna a helyszíni Active Directory módosításainak szinkronizálása az Azure AD szolgáltatásba.

### Az Azure AD Connect engedélyezése (új kiszolgáló)
A telepítést követően az Azure AD Connect újraindításával további konfigurációmódosításokat hajthat végre. Indítsa el az **Azure AD Connectet** a Start menüből vagy a parancsikonnal az asztalról. Győződjön meg róla, hogy nem a telepítő MSI-t próbálja ismét futtatni.

A következőnek kell megjelennie:

![További feladatok](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

- Válassza a **Configure staging mode** (Átmeneti mód konfigurálása) lehetőséget.
- Kapcsolja ki az átmeneti módot az **Enabled staging mode** (Átmeneti mód engedélyezve) jelölőnégyzet jelölésének törlésével.

![Adja meg Azure AD hitelesítő adatait](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

- Kattintson a **Next** (Tovább) gombra
- A jóváhagyó lapon kattintson az **Install** (Telepítés) gombra.

Mostantól az Azure AD Connect az aktív kiszolgáló.

## További lépések
Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](active-directory-aadconnect-whats-next.md).

Ismerkedjen meg a következő, a telepítéssel engedélyezett új szolgáltatásokkal: az [Automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md), a [Véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) és az [Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](active-directory-aadconnectsync-feature-scheduler.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

## Kapcsolódó dokumentáció

Témakör |  
--------- | ---------
Az Azure AD Connect áttekintése | [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
Frissítés egy korábbi Connect-verzióról | [Frissítés a Connect egy korábbi verziójáról](active-directory-aadconnect-upgrade-previous-version.md)
Telepítés gyorsbeállítások használatával | [Az Azure AD Connect gyorstelepítése](active-directory-aadconnect-get-started-express.md)
Telepítés testreszabott beállítások használatával | [Az Azure AD Connect testreszabott telepítése](active-directory-aadconnect-get-started-custom.md)
Telepítési fiókok | [További információk az Azure AD Connect-fiókokkal és -engedélyekkel kapcsolatban](active-directory-aadconnect-accounts-permissions.md)



<!--HONumber=Jun16_HO2-->


