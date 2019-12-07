---
title: 'Azure AD Connect: az Office 365-erőforrások előnyben részesített adatelérési helyének konfigurálása'
description: Ismerteti, hogyan helyezhetők el az Office 365 felhasználói erőforrásai a felhasználóhoz Azure Active Directory Connect szinkronizálással.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0a1d3de1b3eb5aebd89e6601f95c449851d4a1a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889577"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure Active Directory Connect Sync: az Office 365-erőforrások előnyben részesített adatelérési helyének konfigurálása
Ennek a témakörnek a célja, hogy megtudja, hogyan konfigurálhatja az attribútumot az előnyben részesített adathelyhez az Azure Active Directory (Azure AD) kapcsolódási szinkronizálásban. Ha valaki multi-geo képességeket használ az Office 365-ben, ezzel az attribútummal jelölheti meg a felhasználó Office 365-beli adatmennyiségének földrajzi helyét. (A feltételek *régiója* és a *földrajzi* terület szinonimaként használható.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Az előnyben részesített adathely szinkronizálásának engedélyezése
Alapértelmezés szerint a felhasználók Office 365-erőforrásai ugyanabban a földrajzi helyen találhatók, mint az Azure AD-bérlő. Ha például a bérlő a Észak-Amerikaban található, akkor a felhasználók Exchange-postaládái a Észak-Amerikaban is megtalálhatók. A többnemzetiségű szervezetek esetében ez nem lehet optimális.

A **preferredDataLocation**attribútum beállításával megadhatja a felhasználó földrajzi elhelyezkedését. A felhasználó Office 365-erőforrásait, például a postaládát és a OneDrive a felhasználóval megegyező földrajzi tartományba helyezheti, és továbbra is rendelkezik egy Bérlővel a teljes szervezet számára.

> [!IMPORTANT]
> A multi-geo jelenleg aktív Nagyvállalati Szerződés és legalább 500 Office 365 Services-előfizetéssel rendelkező ügyfelek számára érhető el. A részletekért forduljon a Microsoft képviselőjéhez.
>
>

Az Office 365 összes térségek listája megtalálható [ott, ahol az Ön adatai találhatók?](https://aka.ms/datamaps).

A térségek az Office 365-ben több geo esetén is elérhető:

| Geo (Térség) | preferredDataLocation érték |
| --- | --- |
| Ázsia és a Csendes-óceáni térség | APC |
| Ausztrália | Au |
| Kanada | LEHET |
| Európai Unió | EUR |
| Franciaország | FRA |
| India | IND |
| Japán | JPN |
| Dél-Korea | KOR |
| Dél-Afrika | ZAF |
| Egyesült Arab Emírségek | A |
| Egyesült Királyság | GBR |
| Egyesült Államok | NAM |

* Ha a Geo nem szerepel a táblázatban (például Dél-Amerika), akkor nem használható több földrajzi helyhez.

* Nem minden Office 365-alapú számítási feladat támogatja a felhasználó földrajzi elhelyezkedésének használatát.

### <a name="azure-ad-connect-support-for-synchronization"></a>A szinkronizálás Azure AD Connect támogatása

Azure AD Connect támogatja a **preferredDataLocation** attribútum szinkronizálását a 1.1.524.0 vagy újabb verzióban lévő **felhasználói** objektumokhoz. Konkrétan:

* Az Azure AD-összekötőben az objektumtípus- **felhasználó** sémája ki van bővítve a **preferredDataLocation** attribútum belefoglalásával. Az attribútum típusa, egyértékű karakterlánc.
* A metaverse-beli objektumtípus- **személy** sémája ki van bővítve, hogy tartalmazza a **preferredDataLocation** attribútumot. Az attribútum típusa, egyértékű karakterlánc.

Alapértelmezés szerint a **preferredDataLocation** nincs engedélyezve a szinkronizáláshoz. Ez a funkció nagyobb szervezetek számára készült. A Windows Server 2019 Active Directory sémája az **msDS-preferredDataLocation** attribútumot használja erre a célra. Ha nem frissítette a Active Directory sémát, és nem tudja megtenni, akkor meg kell adnia egy attribútumot, amely az Office 365 geo-t fogja tárolni a felhasználók számára. Ez minden szervezet esetében eltérő lesz.

> [!IMPORTANT]
> Az Azure AD lehetővé teszi, hogy a **felhőalapú felhasználói objektumok** **preferredDataLocation** attribútuma közvetlenül az Azure ad PowerShell használatával legyen konfigurálva. Az Azure AD már nem teszi lehetővé a **szinkronizált felhasználói objektumok** **preferredDataLocation** attribútumának közvetlen konfigurálását az Azure ad PowerShell használatával. Ha ezt az attribútumot **szinkronizált felhasználói objektumokon**szeretné konfigurálni, akkor a Azure ad Connectt kell használnia.

A szinkronizálás engedélyezése előtt:

* Ha nem frissítette a Active Directory sémát a 2019-re, akkor döntse el, hogy melyik helyszíni Active Directory attribútumot szeretné használni a forrás attribútumként. A típusnak **egyértékű sztringnek**kell lennie.
* Ha korábban már konfigurálta az **preferredDataLocation** attribútumot az Azure ad-ben meglévő **szinkronizált felhasználói objektumokon** az Azure ad PowerShell használatával, akkor az vezetnie a helyszíni Active Directory megfelelő **felhasználói** objektumaira kell beállítania.

    > [!IMPORTANT]
    > Ha nem vezetnie ezeket az értékeket, Azure AD Connect eltávolítja a meglévő attribútum-értékeket az Azure AD-ben, ha engedélyezve van a **preferredDataLocation** attribútum szinkronizálása.

* Konfigurálja a forrás attribútumot legalább néhány helyszíni Active Directory felhasználói objektumon. Ezt később is használhatja ellenőrzésre.

A következő szakaszokban ismertetjük a **preferredDataLocation** attribútum szinkronizálásának engedélyezéséhez szükséges lépéseket.

> [!NOTE]
> A lépéseket az egyerdős topológiával rendelkező Azure AD-telepítés kontextusában, valamint egyéni szinkronizálási szabályok nélkül ismertetjük. Ha többerdős topológiával rendelkezik, az egyéni szinkronizálási szabályok konfigurálva vannak, vagy átmeneti kiszolgálóval rendelkezik, a lépéseket ennek megfelelően kell módosítania.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1\. lépés: az ütemező szinkronizálásának letiltása és annak ellenőrzése, hogy nincs-e folyamatban szinkronizálás
Ha el szeretné kerülni az Azure AD-ba való nem kívánt módosítások elkerülését, győződjön meg arról, hogy a szinkronizálási szabályok frissítése közben nem történik szinkronizálás. A beépített szinkronizálási ütemező letiltása:

1. Indítsa el a PowerShell-munkamenetet a Azure AD Connect-kiszolgálón.
2. Az ütemezett szinkronizálás letiltása a következő parancsmag futtatásával: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. A **synchronization Service Manager** elindításához **nyissa meg > ** **szinkronizációs szolgáltatást**.
4. Válassza az **Operations (műveletek** ) fület, és ellenőrizze, hogy nincs *-e folyamatban*állapotú művelet.

![Képernyőkép a Synchronization Service Managerról](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>2\. lépés: az Active Directory sémájának frissítése
Ha frissítette a Active Directory sémát a 2019-re, és a kapcsolat a séma kiterjesztése előtt lett telepítve, akkor a kapcsolódási séma gyorsítótára nem rendelkezik a frissített sémával. Ezután frissítenie kell a sémát a varázslóból, hogy az megjelenjen a felhasználói felületen.

1. Indítsa el a Azure AD Connect varázslót az asztalról.
2. Válassza a **címtár-séma frissítése** lehetőséget, majd kattintson a **tovább**gombra.
3. Adja meg az Azure AD-beli hitelesítő adatait, és kattintson a **tovább**gombra.
4. A **címtár-séma frissítése** lapon győződjön meg arról, hogy az összes erdő ki van választva, majd kattintson a **tovább**gombra.
5. Ha elkészült, a varázsló bezárásával.

![Képernyőkép a címtár-séma frissítése a kapcsolat varázslóban](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>3\. lépés: a forrás attribútum hozzáadása a helyszíni Active Directory összekötő sémához
**Erre a lépésre csak akkor van szükség, ha a 1.3.21 vagy régebbi verziójú csatlakozási verziót futtatja. Ha a 1.4.18 vagy újabb verzióval rendelkezik, ugorjon az 5. lépésre.**  
Nem minden Azure AD-attribútum importálható a helyszíni Active Directory-összekötő területére. Ha olyan attribútumot jelölt ki, amely alapértelmezés szerint nincs szinkronizálva, akkor importálnia kell azt. A forrás attribútum hozzáadása az importált attribútumok listájához:

1. Válassza a Synchronization Service Manager **Összekötők** lapját.
2. Kattintson a jobb gombbal a helyszíni Active Directory összekötőre, majd válassza a **Tulajdonságok**lehetőséget.
3. Az előugró párbeszédpanelen lépjen az **attribútumok kiválasztása** lapra.
4. Győződjön meg arról, hogy a használni kívánt forrásoldali attribútum be van jelölve az attribútumok listájában. Ha nem látja az attribútumot, jelölje be az **összes megjelenítése** jelölőnégyzetet.
5. A mentéshez kattintson **az OK gombra**.

![A Synchronization Service Manager és a Tulajdonságok párbeszédpanel képernyőképe](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>4\. lépés: **PreferredDataLocation** hozzáadása az Azure ad Connector sémához
**Erre a lépésre csak akkor van szükség, ha a 1.3.21 vagy régebbi verziójú csatlakozási verziót futtatja. Ha a 1.4.18 vagy újabb verzióval rendelkezik, ugorjon az 5. lépésre.**  
Alapértelmezés szerint a **preferredDataLocation** attribútum nem lett importálva az Azure ad-összekötő területére. Hozzáadás az importált attribútumok listájához:

1. Válassza a Synchronization Service Manager **Összekötők** lapját.
2. Kattintson a jobb gombbal az Azure AD-összekötőre, és válassza a **Tulajdonságok**lehetőséget.
3. Az előugró párbeszédpanelen lépjen az **attribútumok kiválasztása** lapra.
4. Válassza ki a **preferredDataLocation** attribútumot a listában.
5. A mentéshez kattintson **az OK gombra**.

![A Synchronization Service Manager és a Tulajdonságok párbeszédpanel képernyőképe](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>5\. lépés: bejövő szinkronizálási szabály létrehozása
A bejövő szinkronizálási szabály lehetővé teszi, hogy az attribútum értéke a helyszíni Active Directory Forrás attribútumáról a metaverse-ba kerüljön.

1. Indítsa el a **szinkronizálási szabályok szerkesztőjét** , és **indítsa** el > **szinkronizálási szabályok szerkesztőjét**.
2. Állítsa be a keresési szűrő **irányát** **bejövő**értékre.
3. Új bejövő szabály létrehozásához válassza az **új szabály hozzáadása**lehetőséget.
4. A **Leírás** lapon adja meg a következő konfigurációt:

    | Attribútum | Value (Díj) | Részletek |
    | --- | --- | --- |
    | Név | *Adjon meg egy nevet* | Például: "az AD-User preferredDataLocation" |
    | Leírás | *Egyéni Leírás megadása* |  |
    | Csatlakoztatott rendszerek | *A helyszíni Active Directory-összekötő kiválasztása* |  |
    | Csatlakoztatott rendszerobjektum típusa | **Felhasználó** |  |
    | Metaverse objektum típusa | **Személy** |  |
    | Hivatkozás típusa | **Csatlakozás** |  |
    | Prioritás | *Válasszon egy 1 – 99 közötti számot* | az 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Ne válasszon olyan értéket, amelyet egy másik szinkronizálási szabály használ. |

5. Az összes objektum belefoglalásához hagyja üresen a **hatókör-szűrőt** . Előfordulhat, hogy a Azure AD Connect központi telepítésének megfelelően kell megcsípése a hatókör-szűrőt.
6. Nyissa meg az **átalakítás lapot**, és hajtsa végre a következő átalakítási szabályt:

    | Folyamat típusa | Cél attribútum | Forrás | Egyszeri alkalmazás | Egyesítés típusa |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | A forrás attribútum kiválasztása | Nincs bejelölve | Frissítés |

7. A Bejövő szabály létrehozásához válassza a **Hozzáadás**lehetőséget.

![Képernyőkép a bejövő szinkronizálási szabály létrehozásáról](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>6\. lépés: kimenő szinkronizálási szabály létrehozása
A kimenő szinkronizálási szabály lehetővé teszi, hogy az attribútum értéke a metaverse-ből a **preferredDataLocation** attribútumba kerüljön az Azure ad-ben:

1. Nyissa meg a **szinkronizálási szabályok szerkesztőjét**.
2. Állítsa a keresési szűrő **irányát** **kimenő**értékre.
3. Válassza az **új szabály hozzáadása**lehetőséget.
4. A **Leírás** lapon adja meg a következő konfigurációt:

    | Attribútum | Value (Díj) | Részletek |
    | ----- | ------ | --- |
    | Név | *Adjon meg egy nevet* | Például: "az Azure AD – User preferredDataLocation" |
    | Leírás | *Adja meg a leírást* ||
    | Csatlakoztatott rendszerek | *Válassza ki az Azure AD-összekötőt* ||
    | Csatlakoztatott rendszerobjektum típusa | **Felhasználó** ||
    | Metaverse objektum típusa | **Személy** ||
    | Hivatkozás típusa | **Csatlakozás** ||
    | Prioritás | *Válasszon egy 1 – 99 közötti számot* | az 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Ne válasszon olyan értéket, amelyet egy másik szinkronizálási szabály használ. |

5. Nyissa meg a **hatókör-szűrő** lapot, és adjon hozzá egyetlen hatókörű szűrőt két záradékkal:

    | Attribútum | Művelet | Value (Díj) |
    | --- | --- | --- |
    | sourceObjectType | EGYENLŐ | Felhasználó |
    | cloudMastered | NOTEQUAL | Igaz |

    A hatóköri szűrő határozza meg, hogy mely Azure AD-objektumokat alkalmazza a kimenő szinkronizálási szabály. Ebben a példában ugyanazt a hatókör-szűrőt használjuk a "kimenő az Azure AD – felhasználói identitás" OOB (beépített) szinkronizálási szabálya alapján. Megakadályozza, hogy a szinkronizálási szabály olyan **felhasználói** objektumokra legyen alkalmazva, amelyek nincsenek szinkronizálva a helyszíni Active Directory. Előfordulhat, hogy a Azure AD Connect központi telepítésének megfelelően kell megcsípése a hatókör-szűrőt.

6. Nyissa meg az **átalakítás** lapot, és hajtsa végre a következő átalakítási szabályt:

    | Folyamat típusa | Cél attribútum | Forrás | Egyszeri alkalmazás | Egyesítés típusa |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | Nincs bejelölve | Frissítés |

7. Zárja be a **Hozzáadás** lehetőséget a Kimenő szabály létrehozásához.

![Képernyőkép a kimenő szinkronizálási szabály létrehozásáról](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>7\. lépés: teljes szinkronizálási ciklus futtatása
Általánosságban elmondható, hogy teljes szinkronizálási ciklusra van szükség. Ennek az az oka, hogy új attribútumok lettek hozzáadva mind a Active Directory, mind az Azure AD Connector sémához, és egyéni szinkronizálási szabályokat vezetett be. Győződjön meg arról, hogy az Azure AD-ba való exportálás előtt ellenőrzi a módosításokat. A következő lépésekkel ellenőrizheti a módosításokat, miközben manuálisan futtathatja a teljes szinkronizálási ciklust alkotó lépéseket.

1. **Teljes importálás** futtatása a helyszíni Active Directory-összekötőn:

   1. Lépjen a Synchronization Service Manager **Operations (műveletek** ) lapjára.
   2. Kattintson a jobb gombbal a helyszíni **Active Directory-összekötőre**, majd válassza a **Futtatás**lehetőséget.
   3. A párbeszédpanelen válassza a **teljes importálás**lehetőséget, majd kattintson **az OK gombra**.
   4. Várjon, amíg a művelet befejeződik.

      > [!NOTE]
      > Ha a forrás attribútum már szerepel az importált attribútumok listájában, kihagyhatja a teljes importálást a helyszíni Active Directory-összekötőn. Ez azt jelenti, hogy a cikk korábbi, 2. lépésében nem kellett módosítania a módosításokat.

2. **Teljes importálás** futtatása az Azure ad-összekötőn:

   1. Kattintson a jobb gombbal az **Azure ad-összekötőre**, majd válassza a **Futtatás**lehetőséget.
   2. A párbeszédpanelen válassza a **teljes importálás**lehetőséget, majd kattintson **az OK gombra**.
   3. Várjon, amíg a művelet befejeződik.

3. Ellenőrizze, hogy a szinkronizálási szabály megváltozása egy meglévő **felhasználói** objektumon történt-e.

   A helyszíni Active Directory és az Azure AD **preferredDataLocation** származó forrásoldali attribútum a megfelelő összekötő-területekre lett importálva. Mielőtt továbblép a teljes szinkronizálás lépésre, végezze el a helyszíni Active Directory-összekötő területének meglévő **felhasználói** objektumának előzetes verzióját. A kiválasztott objektumnak a forrás attribútummal kell rendelkeznie. A metaverse-ben feltöltött **preferredDataLocation** sikeres előzetes verziója jól jelzi, hogy helyesen konfigurálta a szinkronizálási szabályokat. További információ az előzetes verzióról: [a módosítás ellenőrzése](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. **Teljes szinkronizálás** futtatása a helyszíni Active Directory-összekötőn:

   1. Kattintson a jobb gombbal a helyszíni **Active Directory-összekötőre**, majd válassza a **Futtatás**lehetőséget.
   2. A párbeszédpanelen válassza a **teljes szinkronizálás**lehetőséget, majd kattintson **az OK gombra**.
   3. Várjon, amíg a művelet befejeződik.

5. Az Azure AD-re **irányuló függőben lévő exportálás** ellenőrzése:

   1. Kattintson a jobb gombbal az **Azure ad-összekötőre**, majd válassza az **összekötő terület keresése**lehetőséget.
   2. A **keresési összekötő területe** párbeszédpanelen:

        a. **Hatókör** beállítása **függőben lévő exportálásra**.<br>
        b. Jelölje be mind a három jelölőnégyzetet, beleértve a **Hozzáadás, a módosítás és a törlés**lehetőséget.<br>
        c. Az exportálandó módosításokat tartalmazó objektumok listájának megtekintéséhez válassza a **Keresés**lehetőséget. Egy adott objektum változásainak vizsgálatához kattintson duplán az objektumra.<br>
        d. Ellenőrizze, hogy a módosítások várhatóak-e.

6. **Exportálás** futtatása az **Azure ad Connectoron**

   1. Kattintson a jobb gombbal az **Azure ad-összekötőre**, majd válassza a **Futtatás**lehetőséget.
   2. Az **összekötő futtatása** párbeszédpanelen válassza az **Exportálás**lehetőséget, majd kattintson **az OK gombra**.
   3. Várjon, amíg a művelet befejeződik.

> [!NOTE]
> Észreveheti, hogy a lépések nem tartalmazzák a teljes szinkronizálás lépést az Azure AD-összekötőn, vagy az Exportálás lépését az Active Directory-összekötőn. A lépések nem szükségesek, mert az attribútumérték csak a helyszíni Active Directoryról az Azure AD-re áramlik.

## <a name="step-8-re-enable-sync-scheduler"></a>8\. lépés: a szinkronizálási ütemező újbóli engedélyezése
Engedélyezze újra a beépített szinkronizálási ütemező funkciót:

1. Indítsa el a PowerShell-munkamenetet.
2. Engedélyezze újra az ütemezett szinkronizálást a következő parancsmag futtatásával: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>8\. lépés: az eredmény ellenőrzése
Itt az ideje, hogy ellenőrizze a konfigurációt, és engedélyezze azt a felhasználók számára.

1. Adja hozzá a Geo-t a kiválasztott attribútumhoz a felhasználónál. Az elérhető térségek listája ebben a táblázatban található.  
![A felhasználóhoz hozzáadott AD-attribútum képernyőképe](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Várjon, amíg az attribútum szinkronizálva lesz az Azure AD-vel.
3. Az Exchange Online PowerShell használatával ellenőrizze, hogy a postaláda-régió helyesen van-e beállítva.  
![Képernyőfelvétel az Exchange Online PowerShellről](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Ha a bérlőt úgy jelölte meg, hogy használni tudja ezt a funkciót, a rendszer áthelyezi a postaládát a megfelelő földrajzi helyre. Ennek ellenőrzéséhez tekintse meg a kiszolgáló nevét, ahol a postaláda található.
4. Annak ellenőrzéséhez, hogy ez a beállítás több postaláda esetében is érvényben van-e, használja a [TechNet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e)-katalógusban található parancsfájlt. Ez a szkript tartalmazza az Office 365-adatközpontok kiszolgálói előtagjainak listáját is, valamint azt, hogy mely geo-ben található. Az előző lépésben hivatkozásként is használható a postaláda helyének ellenőrzéséhez.

## <a name="next-steps"></a>Következő lépések

További információ az Office 365-ben elérhető multi-geo szolgáltatásról:

* [Multi-geo munkamenetek az Ignite-on](https://aka.ms/MultiGeoIgnite)
* [Multi-geo a OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Multi-geo a SharePoint Online-ban](https://aka.ms/SharePointMultiGeo)

További információ a konfigurációs modellről a Szinkronizáló motorban:

* További információ a konfigurációs modellről a [deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md)című cikkből.
* További információ a kifejezés nyelvéről a [deklaratív kiépítési kifejezések ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)című cikkből.

Áttekintő témakörök:

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
