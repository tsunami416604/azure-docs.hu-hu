---
title: 'Az Azure Active Directory Connect szinkronizálási szolgáltatás: Az Office 365-ben adatainak előnyben részesített helye Multi-földrajzi funkciók konfigurálása |} A Microsoft Docs'
description: Útmutató az Office 365 felhasználói erőforrásokat a felhasználó Azure Active Directory Connect-szinkronizálással közelében helyezze.
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
ms.date: 07/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: f03fc201c1bce3187fdbd25c1de3567170478d30
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488438"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Az Azure Active Directory Connect szinkronizálási szolgáltatás: Konfigurálja az Office 365-erőforrások adatainak előnyben részesített helye
Ez a témakör az a célja, hogy végigvezetik az attribútum a adatainak előnyben részesített helye konfigurálása az Azure Active Directory (Azure AD) Connect-szinkronizálás. Amikor valaki használ Multi-földrajzi funkciókat az Office 365-ben, ez az attribútum használatával a földrajzi helymeghatározás, a felhasználó Office 365-adatokat jelöl ki. (A használati *régió* és *földrajzi* azonos értelemben használják.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Elsődleges adathelyként szinkronizálásának engedélyezése
Alapértelmezés szerint az Office 365-erőforrások a felhasználók számára, az Azure AD-bérlője ugyanabban a földrajzi találhatók. Például ha a bérlő Észak-Amerikában található, majd a felhasználó Exchange-postaládák is található Észak-Amerikában. Egy nemzetközi szervezet esetén ez nem feltétlenül optimális.

Az attribútum beállításával **preferredDataLocation**, meghatározhatja, hogy a felhasználó földrajzi. A felhasználó Office 365 erőforrások, például a postaláda és a onedrive vállalati verzió, a felhasználó ugyanabban a földrajzi rendelkezik, és továbbra is fennáll a teljes szervezet számára több bérlőhöz.

> [!IMPORTANT]
> Multi-földrajzi érhető el jelenleg az Office 365-szolgáltatásokhoz az előfizetések 2500 legalább rendelkező ügyfelek számára. Forduljon a Microsoft helyi képviselőjéhez részleteiről.
>
>

Az Office 365 összes helyeken listája található [hol van az adatok?](https://aka.ms/datamaps).

Az Office 365-ben elérhető Multi-földrajzi helyeken a következők:

| Geo (Térség) | preferredDataLocation érték |
| --- | --- |
| Ázsia és a Csendes-óceáni térség | APC |
| Ausztrália | AZ AU-K |
| Kanada | IS |
| Európai Unió | EUR |
| Franciaország | FRA |
| India | IND |
| Japán | JPN |
| Korea | KOR |
| Egyesült Királyság | GBR |
| Egyesült Államok | NAM |

* Ha egy-egy térségen nem szerepel ebben a táblában (például a Dél-Amerika), majd azt nem használható a több-a földrajzi.

* Nem minden Office 365 számítási feladattal a felhasználó földrajzi beállítás használatát támogatja.

### <a name="azure-ad-connect-support-for-synchronization"></a>Szinkronizálás az Azure AD Connect támogatása

Azure AD Connect szinkronizálásának támogatja a **preferredDataLocation** az attribútum **felhasználói** verziójában 1.1.524.0 objektumok és újabb verziók. Konkrétan:

* Az objektumtípus sémája **felhasználói** az Azure AD Connectoron kibővül: közé tartozik a **preferredDataLocation** attribútum. Az attribútum a egyértékű karakterlánc típusú.
* Az objektumtípus sémája **személy** metaverzumban található kell terjeszteni a **preferredDataLocation** attribútum. Az attribútum a egyértékű karakterlánc típusú.

Alapértelmezés szerint **preferredDataLocation** szinkronizálás nincs engedélyezve. Ez a funkció a nagyobb vállalatok szól. Is meg kell adnia egy attribútum, amely tárolja a felhasználók számára az Office 365 földrajzi, mert nincs **preferredDataLocation** a helyszíni Active Directory-attribútumban. Itt kapnak majd minden egyes vállalat esetében eltérő lehet.

> [!IMPORTANT]
> Az Azure AD lehetővé teszi, hogy a **preferredDataLocation** attribútum **a felhő felhasználói objektumok** közvetlenül kell konfigurálni az Azure AD PowerShell használatával. Azure ad-ben már nem engedélyezi a **preferredDataLocation** attribútum **felhasználói objektumok szinkronizálva** közvetlenül kell konfigurálni az Azure AD PowerShell használatával. Ez az attribútum konfigurálása **felhasználói objektumok szinkronizálva**, az Azure AD Connect kell használnia.

A szinkronizálás engedélyezése előtt:

* Döntse el, mely a helyszíni Active Directory attribútumát, az adatforrás-attribútum használható. A típusúnak kell lennie **egyértékű karakterlánc**. A következő, egy lépések a **extensionAttributes** szolgál.
* Ha korábban konfigurálta a **preferredDataLocation** attribútum a meglévő **felhasználói objektumok szinkronizálva** az Azure AD PowerShell-lel az Azure AD-ben az attribútumértékek backport kell a megfelelő **felhasználói** objektumokat a helyszíni Active Directoryban.

    > [!IMPORTANT]
    > Ha nem tesz nem backport ezeket az értékeket, az Azure AD Connect a meglévő attribútumértékek eltávolítja az Azure AD-ben amikor a szinkronizálás a **preferredDataLocation** engedélyezve van.

* Az adatforrás-attribútum már legalább egy pár a helyszíni Active Directory felhasználói objektumok konfigurálása. Ezzel az ellenőrzéshez később.

A következő szakaszok a lépéseket a szinkronizálás engedélyezése a **preferredDataLocation** attribútum.

> [!NOTE]
> A lépéseket a környezetben az Azure AD központi egyerdős topológiával, valamint anélkül, hogy egyéni szinkronizálási szabályait ismerteti. Ha a többerdős topológiákat, egyéni szinkronizálási szabályok konfigurálva, vagy egy átmeneti kiszolgálón rendelkezik, ennek megfelelően kell módosítania a lépéseket.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés: Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
Az Azure AD-exportálás alatt álló nem szándékos módosítások elkerülése érdekében győződjön meg arról, szinkronizálás nem kerül sor közepén szinkronizálási szabályok frissítése közben. A beépített szinkronizálásütemező letiltása:

1. Indítsa el egy PowerShell-munkamenetet a az Azure AD Connect-kiszolgáló.
2. Ez a parancsmag futtatásával ütemezett szinkronizálás letiltása: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Indítsa el a **Synchronization Service Managert** a **START** > **szinkronizálási szolgáltatás**.
4. Válassza ki a **műveletek** fülre, és ellenőrizze, nincs művelet állapotú *folyamatban*.

![A Synchronization Service Managert képernyőképe](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>2. lépés: Az adatforrás-attribútum hozzáadása a helyszíni Active Directory-összekötő séma
Nem minden Azure AD-attribútumok is importálja a helyszíni Active Directory-összekötő területet. Ha a kiválasztott használatához olyan attribútum, amely alapértelmezés szerint nincs szinkronizálva, majd, importálnia kell. Az adatforrás-attribútum hozzáadása az importált attribútumok listáját:

1. Válassza ki a **összekötők** fülre a Synchronization Service Managert.
2. Kattintson a jobb gombbal a helyszíni Active Directory-összekötőt, és válassza ki **tulajdonságok**.
3. A megjelenő párbeszédpanelen nyissa meg a **attribútumok kiválasztása** fülre.
4. Ellenőrizze, hogy a kiválasztott használandó adatforrás-attribútum be van jelölve, a attribútum listában. Ha nem látja a kívánt attribútum, válassza ki a **összes megjelenítése** jelölőnégyzetet.
5. Mentéséhez válassza **OK**.

![Képernyőkép – a Synchronization Service Managert és tulajdonságai párbeszédpanel](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>3. lépés: Adjon hozzá **preferredDataLocation** az Azure AD-összekötő séma
Alapértelmezés szerint a **preferredDataLocation** attribútum nem importálja az Azure AD összekötőterében. Az importált attribútumlistát, hozzáadása:

1. Válassza ki a **összekötők** fülre a Synchronization Service Managert.
2. Kattintson a jobb gombbal az Azure AD-összekötő, és válassza ki **tulajdonságok**.
3. A megjelenő párbeszédpanelen nyissa meg a **attribútumok kiválasztása** fülre.
4. Válassza ki a **preferredDataLocation** attribútum a listában.
5. Mentéséhez válassza **OK**.

![Képernyőkép – a Synchronization Service Managert és tulajdonságai párbeszédpanel](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>4. lépés: Bejövő szinkronizálási szabály létrehozása
A bejövő szinkronizálási szabály lehetővé teszi, hogy az attribútum értéke a helyszíni Active Directoryban a forrásattribútum áramlását a metaverzumba.

1. Indítsa el a **szinkronizálási Szabályszerkesztővel** a **START** > **szinkronizálási Szabályszerkesztővel**.
2. Állítsa be a keresési szűrő **iránya** kell **bejövő**.
3. Egy új bejövő szabály létrehozásához válassza **új szabály hozzáadása**.
4. Alatt a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például "az ad-felhasználó preferredDataLocation" |
    | Leírás | *Adjon meg egy egyéni leírást* |  |
    | Csatlakoztatott rendszer | *Válassza ki a helyszíni Active Directory-összekötő* |  |
    | Csatlakoztatott rendszer objektum típusa | **Felhasználó** |  |
    | Metaverzum-objektum típusa | **Személy** |  |
    | Hivatkozás típusa | **Csatlakozás** |  |
    | Sorrend | *1 – 99 közötti számot válasszon* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt értéket. |

5. Tartsa a **Scoping szűrő** üres, az összes objektumát tartalmazza. Akkor lehet, hogy módosítania kell a Hatókörszűrő megfelelően az Azure AD Connect üzemelő példány.
6. Nyissa meg a **átalakítási lapon**, és a következő átalakítási szabály végrehajtása:

    | Folyamat típusát | Célattribútum | Forrás | Miután a alkalmazni | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    |Közvetlen | preferredDataLocation | Válassza ki az adatforrás-attribútum | Nincs bejelölve | Frissítés |

7. A bejövő szabály létrehozásához válassza **Hozzáadás**.

![Bejövő szinkronizálási szabály létrehozása képernyőképe](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>5. lépés: Kimenő szinkronizálási szabály létrehozása
A kimenő szinkronizálási szabály lehetővé teszi a metaverzumba, hogy a flow kívánt attribútumértéket a **preferredDataLocation** attribútum az Azure ad-ben:

1. Nyissa meg a **szinkronizálási Szabályszerkesztő**.
2. Állítsa be a keresési szűrő **iránya** kell **kimenő**.
3. Válassza ki **új szabály hozzáadása**.
4. Alatt a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | ----- | ------ | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például "ki az Azure AD-felhasználó preferredDataLocation" |
    | Leírás | *Adjon meg egy leírást* ||
    | Csatlakoztatott rendszer | *Válassza ki az Azure AD Connectoron* ||
    | Csatlakoztatott rendszer objektum típusa | **Felhasználó** ||
    | Metaverzum-objektum típusa | **Személy** ||
    | Hivatkozás típusa | **Csatlakozás** ||
    | Sorrend | *1 – 99 közötti számot válasszon* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt értéket. |

5. Nyissa meg a **Scoping szűrő** lapra, és két záradékai egy egyetlen hatókörkezelési Szűrőcsoport hozzáadása:

    | Attribútum | Művelet | Érték |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Felhasználó |
    | cloudMastered | NOTEQUAL | True (Igaz) |

    Hatókörszűrő határozza meg, mely az Azure AD-objektumokat a kimenő szinkronizálási szabály vonatkozik. Ebben a példában a "Ki az ad-hez – felhasználói identitást" azonos hatókörszűrőt használjuk (out-of-box) OOB-szinkronizálási szabály. Megakadályozza, hogy a szinkronizálási szabály alkalmazásakor **felhasználói** nem a helyszíni Active Directoryból szinkronizált objektumok. Akkor lehet, hogy módosítania kell a Hatókörszűrő megfelelően az Azure AD Connect üzemelő példány.

6. Nyissa meg a **átalakítási** lapra, és végrehajtja a következő átalakítási szabályt:

    | Folyamat típusát | Célattribútum | Forrás | Miután a alkalmazni | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | preferredDataLocation | preferredDataLocation | Nincs bejelölve | Frissítés |

7. Bezárás **Hozzáadás** a kimenő szabály létrehozásához.

![Kimenő szinkronizálási szabály létrehozása képernyőképe](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>6. lépés: Futtassa a teljes szinkronizálási ciklust
Általában a teljes szinkronizálási ciklust szükség. Ennek az az oka a hozzáadott új attribútumok az Active Directory és az Azure AD-összekötő sémát, és vezetett be egyéni szinkronizálási szabályait. A módosítások ellenőrzéséhez őket az Azure AD-exportálás előtt. A következő lépések segítségével ellenőrizze a módosításokat, a lépések egy teljes szinkronizálási ciklust alkotó manuális futtatása során.

1. Futtatás **teljes importálást** meg a helyszíni Active Directory-összekötőt:

   1. Nyissa meg a **műveletek** fülre a Synchronization Service Managert.
   2. Kattintson a jobb gombbal a **a helyszíni Active Directory-összekötő**, és válassza ki **futtatása**.
   3. A párbeszédpanelen válassza ki a **teljes importálást**, és válassza ki **OK**.
   4. Várjon, amíg a művelet befejeződik.

    > [!NOTE]
    > A helyszíni Active Directory-összekötő a teljes importálást kihagyhatja, ha az adatforrás-attribútum már szerepel az importált attribútumok listáját. Más szóval, nincs semmilyen módosítást a cikk korábbi részeiben 2. lépés során.

2. Futtatás **teljes importálást** az Azure AD Connectoron:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő**, és válassza ki **futtatása**.
   2. A párbeszédpanelen válassza ki a **teljes importálást**, és válassza ki **OK**.
   3. Várjon, amíg a művelet befejeződik.

3. A szinkronizálási szabály módosítások meglévő ellenőrzéséhez **felhasználói** objektum.

   Az adatforrás-attribútum a helyszíni Active Directoryból, és **preferredDataLocation** az Azure ad-ből, importált, minden egyes megfelelő összekötőterében. A teljes szinkronizálás lépés folytatása előtt hajtsa végre a meglévő a preview **felhasználói** objektum a helyszíni Active Directory-összekötő területen. A kiválasztott objektum kell rendelkeznie a forrásattribútum feltöltve. A sikeres előzetes **preferredDataLocation** betöltve a metaverzumba jól jelzi, hogy konfigurálta a szinkronizálási szabályok megfelelően. Előzetes kapcsolatban további információkért lásd: [ellenőrizze a módosítást](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Futtatás **teljes szinkronizálás** a helyszíni Active Directory-összekötőt:

   1. Kattintson a jobb gombbal a **a helyszíni Active Directory-összekötő**, és válassza ki **futtatása**.
   2. A párbeszédpanelen válassza ki a **teljes szinkronizálást**, és válassza ki **OK**.
   3. Várjon, amíg a művelet befejeződik.

5. Győződjön meg arról **függőben lévő Exportálásokról** az Azure ad-hez:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő**, és válassza ki **keresési Összekötőterét**.
   2. Az a **keresési Összekötőterét** párbeszédpanel:

        a. Állítsa be **hatókör** való **függő exportálás**.<br>
        b. Válassza ki az összes a három jelölőnégyzetet, beleértve a **hozzáadásához, módosításához és törléséhez**.<br>
        c. Az objektumok listájában, a változások exportálható megtekintéséhez jelölje ki **keresési**. A módosítások egy adott objektum vizsgálatához kattintson duplán az objektumot.<br>
        d. Győződjön meg arról, hogy a változások várhatók.

6. Futtatás **exportálása** a a **Azure AD Connectoron**

   1. Kattintson a jobb gombbal a **Azure AD-összekötő**, és válassza ki **futtatása**.
   2. Az a **futtatása összekötő** párbeszédpanelen jelölje ki **exportálása**, és válassza ki **OK**.
   3. Várjon, amíg a művelet befejeződik.

> [!NOTE]
> Észreveheti, hogy a lépések nem tartalmazzák a teljes szinkronizálás lépés az Azure AD Connectoron, vagy az Active Directory-összekötő az exportálási lépésben. A lépések nem szükségesek, mert az attribútum értékei csak az Azure ad a helyszíni Active Directoryból átvitele.

## <a name="step-7-re-enable-sync-scheduler"></a>7. lépés: Szinkronizálásütemező újbóli engedélyezése
A beépített szinkronizálásütemező újbóli engedélyezése:

1. Indítsa el egy PowerShell-munkamenetet.
2. Ez a parancsmag futtatásával újraengedélyezését, ütemezett szinkronizálás: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>8. lépés: Az eredmény ellenőrzése
Mostantól, ellenőrizze a konfigurációt, és engedélyezheti a felhasználók számára.

1. Adja hozzá a felhasználó által a kijelölt attribútum a földrajzi. A térségek listája található [Ez a táblázat](#enable-synchronization-of-preferreddatalocation).  
![Képernyőkép egy felhasználó hozzá AD-attribútum](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Várjon, amíg a szinkronizálását az Azure AD-attribútum.
3. Az Exchange Online PowerShell használatával, győződjön meg arról, hogy a postaláda régióban megfelelően van beállítva.  
![Képernyőfelvétel az Exchange Online PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Ha a bérlő lehessen a funkció használatához van megjelölve, a postaláda került a megfelelő földrajzi. Ez a kiszolgálónév megnézzük, ahol a postaláda ellenőrizhető.
4. Annak ellenőrzésére, hogy ez a beállítás keresztül sok postaládák hatékony lett, használja a szkriptet a [TechNet gallery](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Ez a szkript is rendelkezik a kiszolgáló előtag, az összes Office 365 adatközpontok listáját, és milyen földrajzi található a. Használat az előző lépésben referenciaként, ellenőrizze a postaláda helyét.

## <a name="next-steps"></a>További lépések

További információ a Multi-földrajzi az Office 365-ben:

* [Ignite multi-Geo-munkamenetek](https://aka.ms/MultiGeoIgnite)
* [Multi-földrajzi a onedrive-on](https://aka.ms/OneDriveMultiGeo)
* [A SharePoint Online multi-Georedundáns](https://aka.ms/SharePointMultiGeo)

További információ a konfigurációs modell a szinkronizálási motor:

* További információ a konfigurációs modell [ismertetése deklaratív kiépítés](concept-azure-ad-connect-sync-declarative-provisioning.md).
* További információ az a kifejezés nyelv [deklaratív kiépítés kifejezéseinek ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Áttekintő témakör:

* [Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
