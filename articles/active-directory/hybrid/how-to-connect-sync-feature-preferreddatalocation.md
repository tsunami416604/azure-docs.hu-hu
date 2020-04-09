---
title: 'Azure AD Connect: Az Office 365-erőforrások előnyben részesített adathelyének konfigurálása'
description: Ez a témakör azt ismerteti, hogy az Azure Active Directory Connect szinkronizálásával hogyan helyezheti az Office 365 felhasználói erőforrásait a felhasználó közelébe.
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
ms.openlocfilehash: 723411191d0990583d039a0fc9651437480807b4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983262"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure Active Directory Connect szinkronizálása: Az Office 365-erőforrások előnyben részesített adathelyének konfigurálása
Ez a témakör célja, hogy végigvezeti, hogyan konfigurálja az attribútumot az előnyben részesített adatok helyét az Azure Active Directory (Azure AD) Connect szinkronizálás. Ha valaki multi-geo funkciókat használ az Office 365-ben, ezzel az attribútummal kijelölheti a felhasználó Office 365-adatainak földrajzi helyét. (A *régió* és *a földrajzi* terület kifejezések szinonimaként használatosak.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Az előnyben részesített adatok helyének szinkronizálásának engedélyezése
Alapértelmezés szerint a felhasználók Office 365-erőforrásai ugyanabban a földrajzi helyen találhatók, mint az Azure AD-bérlő. Ha például a bérlő Észak-Amerikában található, akkor a felhasználók Exchange-postaládái is Észak-Amerikában találhatók. Egy multinacionális szervezet számára ez nem biztos, hogy optimális.

A **preferredDataLocation**attribútum beállításával megadhatja a felhasználó földrajzi helyét. A felhasználó Office 365-erőforrásait, például a postaládát és a OneDrive-ot a felhasználóval azonos földrajzi helyen használhatja, és továbbra is rendelkezik egy bérlővel a teljes szervezet számára.

> [!IMPORTANT]
> A Multi-Geo jelenleg aktív nagyvállalati szerződéssel és legalább 500 Office 365-előfizetéssel rendelkező ügyfelek számára érhető el. A részletekért forduljon a Microsoft képviselőjéhez.
>
>

Az Office 365 összes geos listája a Hol találhatók [az adatok?](https://aka.ms/datamaps).

Az Office 365-ben a Multi-Geo számára elérhető geos a következő:

| Térség | preferredDataLocation érték |
| --- | --- |
| Ázsia és a Csendes-óceáni térség | Apc |
| Ausztrália | Aus |
| Kanada | Cna |
| Európai Unió | EUR |
| Franciaország | FRA |
| India | IND |
| Japán | JPN |
| Dél-Korea | KOR |
| Dél-afrikai Köztársaság | ZAF között |
| Svájc | Che |
| Egyesült Arab Emírségek | VANNAK |
| Egyesült Királyság | Gbr |
| Egyesült Államok | Nam |

* Ha egy földrajzi hely nem szerepel ebben a táblázatban (például Dél-Amerika), akkor nem használható a Multi-Geo.

* Nem minden Office 365-feladat támogatja a felhasználó földrajzi helyének beállítását.

### <a name="azure-ad-connect-support-for-synchronization"></a>Az Azure AD Connect támogatása a szinkronizáláshoz

Az Azure AD Connect támogatja a **preferredDataLocation** attribútum szinkronizálását az 1.1.524.0-s és újabb verziójú **felhasználói** objektumokhoz. Ezek a következők:

* A séma az objektum típusa **felhasználó** az Azure AD-összekötő ki van bővítve a **preferredDataLocation** attribútum. Az attribútum típusa, egyértékű karakterlánc.
* A Metaverzumban lévő **Személy** objektumtípus sémája a **preferredDataLocation** attribútummal együtt bővül. Az attribútum típusa, egyértékű karakterlánc.

Alapértelmezés szerint **a preferredDataLocation** nincs engedélyezve a szinkronizáláshoz. Ez a funkció nagyobb szervezetek számára készült. A Windows Server 2019 Active Directory-sémája rendelkezik egy **msDS-preferredDataLocation attribútummal,** amelyet erre a célra használnia kell. Ha nem frissítette az Active Directory sémáját, és nem tudja megtenni, akkor azonosítania kell egy attribútumot, amely az Office 365 földrajzi helyének tárolására szolgál a felhasználók számára. Ez minden szervezetnél más lesz.

> [!IMPORTANT]
> Az Azure AD lehetővé teszi, hogy a **preferredDataLocation** attribútum **a felhőbeli felhasználói objektumokon** közvetlenül konfigurálható az Azure AD PowerShell használatával. Az attribútum **konfigurálásához szinkronizált felhasználói objektumokon**az Azure AD Connect et kell használnia.

A szinkronizálás engedélyezése előtt:

* Ha nem frissítette az Active Directory-sémát 2019-re, majd döntse el, hogy melyik helyszíni Active Directory attribútumot használja forrásattribútumként. Meg kell a típusú, **egyértékű string**.
* Ha korábban konfigurálta a **preferredDataLocation** attribútumot az Azure AD meglévő **szinkronizált felhasználói objektumain** az Azure AD PowerShell használatával, akkor az attribútumértékeket vissza kell portolnia a megfelelő **felhasználói** objektumokhoz a helyszíni Active Directoryban.

    > [!IMPORTANT]
    > Ha nem támogatja ezeket az értékeket, az Azure AD Connect eltávolítja a meglévő attribútumértékeket az Azure AD-ben, ha a **preferredDataLocation** attribútum szinkronizálása engedélyezve van.

* Konfigurálja a forrásattribútumot legalább néhány helyszíni Active Directory-felhasználó objektumon. Ezt később is használhatja ellenőrzésre.

A következő szakaszok a **preferredDataLocation** attribútum szinkronizálásának engedélyezéséhez szükséges lépéseket ismertetik.

> [!NOTE]
> A lépéseket egy erdős topológiával rendelkező Azure AD-telepítés környezetében, egyéni szinkronizálási szabályok nélkül ismertetik. Ha többerdős topológiával rendelkezik, egyéni szinkronizálási szabályok vannak konfigurálva, vagy átmeneti kiszolgálóval rendelkezik, ennek megfelelően kell módosítania a lépéseket.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés: A szinkronizálási ütemező letiltása és annak ellenőrzése, hogy nincs-e folyamatban szinkronizálás
Annak elkerülése érdekében, hogy a nem kívánt módosítások exportálása az Azure AD-be, győződjön meg arról, hogy nem történik szinkronizálás történik, miközben a szinkronizálási szabályok frissítése közben történik. A beépített szinkronizálási ütemező letiltása:

1. PowerShell-munkamenet indítása az Azure AD Connect-kiszolgálón.
2. Tiltsa le az ütemezett szinkronizálást a `Set-ADSyncScheduler -SyncCycleEnabled $false`parancsmag futtatásával: .
3. Indítsa el a **szinkronizálási szolgáltatáskezelőt** a **START** > **szinkronizálási szolgáltatás sal.**
4. Jelölje ki a **Műveletek** lapot, és ellenőrizze, hogy nincs-e *folyamatban*lévő állapotú művelet.

![Képernyőkép: Szinkronizálási szolgáltatáskezelő](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>2. lépés: Az Active Directory sémafrissítése
Ha frissítette az Active Directory sémát 2019-re, és a Connect telepítve lett a sémabővítmény előtt, akkor a Connect séma-gyorsítótárnem rendelkezik a frissített sémával. Ezután frissítenie kell a sémát a varázslóból, hogy megjelenjen a felhasználói felületen.

1. Indítsa el az Azure AD Connect varázslót az asztalról.
2. Válassza a **Könyvtárséma frissítése** lehetőséget, majd kattintson a **Tovább**gombra.
3. Adja meg az Azure AD hitelesítő adatait, és kattintson a **Tovább**gombra.
4. A **Címtárséma frissítése** lapon győződjön meg arról, hogy az összes erdő ki van jelölve, és kattintson a **Tovább**gombra.
5. Ha befejeződött, zárja be a varázslót.

![Képernyőkép a Csatlakozás varázsló Címtárséma frissítése varázslóról](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>3. lépés: A forrásattribútum hozzáadása a helyszíni Active Directory-összekötő sémához
**Erre a lépésre csak akkor van szükség, ha a Connect 1.3.21-es vagy újabb verzióját futtatja. Ha 1.4.18-as vagy újabb, akkor ugorjon az 5.**  
Nem minden Azure AD-attribútumok importálva a helyszíni Active Directory-összekötő térbe. Ha olyan attribútum használatát választotta, amely alapértelmezés szerint nincs szinkronizálva, akkor importálnia kell azt. A forrásattribútum hozzáadása az importált attribútumok listájához:

1. Válassza az **Összekötők** lapot a Szinkronizálási szolgáltatáskezelőben.
2. Kattintson a jobb gombbal a helyszíni Active Directory-összekötőre, és válassza a **Tulajdonságok parancsot.**
3. Az előugró ablakban nyissa meg az **Attribútumok kijelölése** lapot.
4. Győződjön meg arról, hogy a használni kívánt forrásattribútum be van jelölve az attribútumlistában. Ha nem látja az attribútumot, jelölje be az **Összes megjelenítése** jelölőnégyzetet.
5. A mentéshez válassza az **OK gombot.**

![Képernyőkép: A Szinkronizálási szolgáltatáskezelő és -tulajdonságok párbeszédpanel](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>4. lépés: **PreferredDataLocation hozzáadása** az Azure AD Connector sémához
**Erre a lépésre csak akkor van szükség, ha a Connect 1.3.21-es vagy újabb verzióját futtatja. Ha 1.4.18-as vagy újabb, akkor ugorjon az 5.**  
Alapértelmezés szerint a **preferredDataLocation** attribútum nem importálja az Azure AD Connector térbe. Az importált attribútumok listájához való hozzáadás:

1. Válassza az **Összekötők** lapot a Szinkronizálási szolgáltatáskezelőben.
2. Kattintson a jobb gombbal az Azure AD-összekötőre, és válassza **a Tulajdonságok parancsot.**
3. Az előugró ablakban nyissa meg az **Attribútumok kijelölése** lapot.
4. Válassza ki a **preferredDataLocation** attribútumot a listában.
5. A mentéshez válassza az **OK gombot.**

![Képernyőkép: A Szinkronizálási szolgáltatáskezelő és -tulajdonságok párbeszédpanel](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>5. lépés: Bejövő szinkronizálási szabály létrehozása
A bejövő szinkronizálási szabály lehetővé teszi, hogy az attribútumértéke az intézményi Active Directory forrásattribútumából a metaverzumba folyjon.

1. Indítsa el a **Szinkronizálási szabályok szerkesztőjét** a **START** > **szinkronizálási szabályok szerkesztőjével.**
2. Állítsa be, hogy az **Irány** keresési szűrő **bejövő**legyen.
3. Új bejövő szabály létrehozásához válassza **az Új szabály hozzáadása**lehetőséget.
4. A **Leírás** lapon adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Név | *Adjon nevet* | Például: "Az AD -tól – Felhasználó preferredDataLocation" |
    | Leírás | *Egyéni leírás biztosítása* |  |
    | Csatlakoztatott rendszer | *A helyszíni Active Directory-összekötő választása* |  |
    | Csatlakoztatott rendszerobjektum-típus | **Felhasználó** |  |
    | Metaverzum objektum típusa | **Személy** |  |
    | Hivatkozás típusa | **Csatlakozás** |  |
    | Precedencia | *Válasszon egy számot 1–99 között* | Az 1–99 egyéni szinkronizálási szabályok számára van fenntartva. Ne válasszon olyan értéket, amelyet egy másik szinkronizálási szabály használ. |

5. Tartsa üresen **a hatókörszűrőt** az összes objektum felvételéhez. Előfordulhat, hogy módosítania kell a hatókörszűrőt az Azure AD Connect központi telepítésének megfelelően.
6. Nyissa meg az **Átalakítás lapot**, és valósítsa meg a következő átalakítási szabályt:

    | Folyamat típusa | Cél attribútum | Forrás | Alkalmazás egyszer | Egyesítés típusa |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | A forrásattribútum kivétele | Nincs bejelölve | Frissítés |

7. A bejövő szabály létrehozásához válassza a **Hozzáadás**lehetőséget.

![Képernyőkép: Bejövő szinkronizálási szabály létrehozása](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>6. lépés: Kimenő szinkronizálási szabály létrehozása
A kimenő szinkronizálási szabály lehetővé teszi, hogy az attribútumérték a metaverzumból az Azure **AD-ben a preferredDataLocation** attribútumba folyjon:

1. Nyissa meg a **Szinkronizálási szabályok szerkesztőjét.**
2. Állítsa be, hogy az **Irány** keresési szűrő **kimenő**legyen.
3. Válassza **az Új szabály hozzáadása**lehetőséget.
4. A **Leírás** lapon adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | ----- | ------ | --- |
    | Név | *Adjon nevet* | Például: "Out to Azure AD – User preferredDataLocation" |
    | Leírás | *Leírás megadása* ||
    | Csatlakoztatott rendszer | *Válassza ki az Azure AD-összekötőt* ||
    | Csatlakoztatott rendszerobjektum-típus | **Felhasználó** ||
    | Metaverzum objektum típusa | **Személy** ||
    | Hivatkozás típusa | **Csatlakozás** ||
    | Precedencia | *Válasszon egy számot 1–99 között* | Az 1–99 egyéni szinkronizálási szabályok számára van fenntartva. Ne válasszon olyan értéket, amelyet egy másik szinkronizálási szabály használ. |

5. Nyissa meg a **Hatókörszűrő** lapot, és adjon hozzá egy két záradékkal rendelkező hatókörszűrő-csoportot:

    | Attribútum | Művelet | Érték |
    | --- | --- | --- |
    | sourceObjectType | Egyenlő | Felhasználó |
    | cloudMastered | NEM EGYENLŐ | True (Igaz) |

    A hatókör-szűrő határozza meg, hogy mely Azure AD-objektumokra alkalmazza ezt a kimenő szinkronizálási szabályt. Ebben a példában ugyanazt a hatókörszűrőt használjuk az "Out to Azure AD – User Identity" OOB (out-of-box) szinkronizálási szabályból. Megakadályozza, hogy a szinkronizálási szabály olyan **felhasználói** objektumokra legyen alkalmazva, amelyek nincsenek szinkronizálva a helyszíni Active Directoryból. Előfordulhat, hogy módosítania kell a hatókörszűrőt az Azure AD Connect központi telepítésének megfelelően.

6. Nyissa meg az **Átalakítás** lapot, és valósítsa meg a következő átalakítási szabályt:

    | Folyamat típusa | Cél attribútum | Forrás | Alkalmazás egyszer | Egyesítés típusa |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | Nincs bejelölve | Frissítés |

7. A **Hozzáadás bezárása** a kimenő szabály létrehozásához.

![Képernyőkép: Kimenő szinkronizálási szabály létrehozása](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>7. lépés: Teljes szinkronizálási ciklus futtatása
Általában teljes szinkronizálási ciklusra van szükség. Ennek az az oka, hogy új attribútumokat adott hozzá az Active Directory és az Azure AD Connector sémához, és egyéni szinkronizálási szabályokat vezetett be. Ellenőrizze a módosításokat, mielőtt exportálja őket az Azure AD-be. A következő lépésekkel ellenőrizheti a módosításokat, miközben manuálisan futtathatja a teljes szinkronizálási ciklust kifutó lépéseket.

1. Futtassa a **teljes importálást** a helyszíni Active Directory-összekötőn:

   1. Nyissa meg a Szinkronizálási szolgáltatáskezelő **Műveletek** lapját.
   2. Kattintson a jobb gombbal a **helyszíni Active Directory-összekötőre,** és válassza a **Futtatás parancsot.**
   3. A párbeszédpanelen válassza a **Teljes importálás**lehetőséget, majd az **OK**gombot.
   4. Várja meg, amíg a művelet befejeződik.

      > [!NOTE]
      > Kihagyhatja a teljes importálást a helyszíni Active Directory-összekötőn, ha a forrásattribútum már szerepel az importált attribútumok listájában. Más szóval, nem kellett semmilyen változtatást a cikk korábbi 2.

2. **Teljes importálás futtatása** az Azure AD-összekötőn:

   1. Kattintson a jobb gombbal az **Azure AD-összekötőre,** és válassza a **Futtatás parancsot.**
   2. A párbeszédpanelen válassza a **Teljes importálás**lehetőséget, majd az **OK**gombot.
   3. Várja meg, amíg a művelet befejeződik.

3. Ellenőrizze, hogy a szinkronizálási szabály megváltozik-e egy meglévő **Felhasználói** objektumon.

   A helyszíni Active Directory forrásattribútuma és az Azure AD-ből származó **preferredDataLocation** lett importálva az egyes összekötőhelyekbe. Mielőtt folytatná a teljes szinkronizálási lépést, végezze el az előnézetet egy meglévő **felhasználói** objektumon a helyszíni Active Directory-összekötő térben. A kiválasztott objektumnak a forrásattribútumot kell kitöltenie. A metaverzumban feltöltött **preferredDataLocation** sikeres előnézet jó jelzője annak, hogy helyesen konfigurálta a szinkronizálási szabályokat. Az előnézet módjáról [a Módosítás ellenőrzése című](how-to-connect-sync-change-the-configuration.md#verify-the-change)témakörben talál további információt.

4. Futtassa a **teljes szinkronizálást** a helyszíni Active Directory-összekötőn:

   1. Kattintson a jobb gombbal a **helyszíni Active Directory-összekötőre,** és válassza a **Futtatás parancsot.**
   2. A párbeszédpanelen válassza a **Teljes szinkronizálás**lehetőséget, majd az **OK**gombot.
   3. Várja meg, amíg a művelet befejeződik.

5. Az Azure AD **függőben lévő exportálásának** ellenőrzése:

   1. Kattintson a jobb gombbal az **Azure AD-összekötőre,** és válassza a **Keresési összekötő térben parancsot.**
   2. A **Keresési összekötő tér** ben párbeszédpanelen:

        a. A **Hatókör beállítása** **függőexportálásra.**<br>
        b. Jelölje be mindhárom jelölőnégyzetet, beleértve a **Hozzáadás, a Módosítás és a Törlés jelölőnégyzetet.**<br>
        c. Az exportálandó módosításokkal rendelkező objektumok listájának megtekintéséhez válassza a **Keresés lehetőséget.** Egy adott objektum módosításának vizsgálatához kattintson duplán az objektumra.<br>
        d. Ellenőrizze, hogy a várt módosítások várhatók-e.

6. **Exportálás futtatása** az **Azure AD-összekötőn**

   1. Kattintson a jobb gombbal az **Azure AD-összekötőre,** és válassza a **Futtatás parancsot.**
   2. Az **Összekötő futtatása** párbeszédpanelen válassza az **Exportálás**lehetőséget, majd az **OK**gombot.
   3. Várja meg, amíg a művelet befejeződik.

> [!NOTE]
> Előfordulhat, hogy a lépések nem tartalmazzák a teljes szinkronizálási lépést az Azure AD-összekötőn, vagy az exportálási lépést az Active Directory-összekötőn. A lépések nem szükségesek, mert az attribútumértékek a helyszíni Active Directoryból csak az Azure AD-be áramlanak.

## <a name="step-8-re-enable-sync-scheduler"></a>8. lépés: Szinkronizálási ütemező újbóli engedélyezése
A beépített szinkronizálási ütemező újbóli engedélyezése:

1. PowerShell-munkamenet indítása.
2. A parancsmag futtatásával engedélyezze újra az ütemezett szinkronizálást:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>9. lépés: Az eredmény ellenőrzése
Itt az ideje, hogy ellenőrizze a konfigurációt, és engedélyezze azt a felhasználók számára.

1. Adja hozzá a földrajzi hely a felhasználó kijelölt attribútumához. Az elérhető földrajzi adatok listája ebben a táblázatban található.  
![Képernyőkép a felhasználóhoz hozzáadott AD attribútumról](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Várja meg, amíg az attribútum szinkronizálódik az Azure AD-vel.
3. Az Exchange Online PowerShell használatával ellenőrizze, hogy a postaláda-régió megfelelően van-e beállítva.  
![Képernyőkép az Exchange Online PowerShellről](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Feltételezve, hogy a bérlő meg van jelölve, hogy képes legyen használni ezt a funkciót, a postaláda a megfelelő földrajzi helyre kerül. Ez a postaláda helye szerinti kiszolgáló nevének megnézésével ellenőrizhető.

## <a name="next-steps"></a>További lépések

További információ az Office 365-ben található Multi-Geo játékról:

* [Multi-Geo ülések ignite](https://aka.ms/MultiGeoIgnite)
* [Többgeos a OneDrive-ban](https://aka.ms/OneDriveMultiGeo)
* [Többgeos a SharePoint Online-ban](https://aka.ms/SharePointMultiGeo)

További információ a konfigurációs modellről a szinkronizálási motorban:

* A konfigurációs modellről a [Deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md)című témakörben olvashat bővebben.
* A kifejezés nyelvéről a [Deklaratív kiépítési kifejezések ismertetése című](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)témakörben olvashat bővebben.

Áttekintő témakörök:

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
