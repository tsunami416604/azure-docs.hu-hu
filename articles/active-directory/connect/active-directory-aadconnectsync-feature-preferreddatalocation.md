---
title: "Azure Active Directory Connect szinkronizálási szolgáltatás: Multi-földrajzi képességeinek elsődleges hely konfigurálása az Office 365-ben |} Microsoft Docs"
description: "Az Office 365 felhasználói erőforrások megközelíti a felhasználó Azure Active Directory Connect szinkronizálási put ismerteti."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: billmath
ms.openlocfilehash: a5ebd61539af7116b8f92cdf9404cd2b5cdea193
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure Active Directory Connect szinkronizálási szolgáltatás: elsődleges hely az Office 365-erőforrások konfigurálása
Ez a témakör célja végigvezetik Önt az attribútum az elsődleges hely beállítása az Azure Active Directory (Azure AD) Connect szinkronizálási szolgáltatás. Ha az Office 365-ben Multi-földrajzi szolgáltatásait valaki használja, ez az attribútum segítségével jelölheti ki az Office 365-adatokat a felhasználó földrajzi helye. (A feltételek *régió* és *földrajzi* van megegyezik.)

> [!IMPORTANT]
> Multi-földrajzi jelenleg előzetes verzió. Ha szeretne részt venni az előzetes programban, lépjen kapcsolatba a Microsoft képviselőjével.
>
>

## <a name="enable-synchronization-of-preferred-data-location"></a>Elsődleges hely szinkronizálásának engedélyezése
Alapértelmezés szerint a felhasználók az Office 365-erőforrások, az Azure AD-bérlő azonos földrajzi találhatók. Például ha a bérlő Észak-Amerika található, majd a felhasználó Exchange-postaládák is található Észak-Amerikában. A nemzetközi szervezetnél ez nem feltétlenül optimális.

Úgy, hogy az attribútum **preferredDataLocation**, meghatározhatja, hogy a felhasználó földrajzi. A felhasználó Office 365 erőforrások, például a postaláda és a onedrive vállalati verzió, a felhasználó ugyanazon földrajzi rendelkezik, és még egy bérlő a teljes szervezet számára.

> [!IMPORTANT]
> Jogosult Multi-földrajzi, rendelkeznie kell legalább 5 000 munkaállomásokat az Office 365-előfizetésben.
>
>

Az Office 365 összes geos listája található [hol található a található adatokhoz?](https://aka.ms/datamaps).

Az Office 365 Multi-földrajzi elérhető geos a következők:

| Geo (Térség) | preferredDataLocation érték |
| --- | --- |
| Ázsia és a Csendes-óceáni térség | APC |
| Ausztrália | AUSZTRÁLIAI |
| Kanada | IS |
| Európai Unió | EUR |
| India | IND |
| Japán | JPN |
| Dél-Korea | KOR |
| Egyesült Királyság | GBR |
| Egyesült Államok | NÉV |

* Ha egy földrajzi nem szerepel ebben a táblában (például Dél-Amerika), akkor azt nem használható a Multi-földrajzi.
* Indiai és dél koreai geos csak ügyfelek számlázási címet, és ezek geos a megvásárolt licencek számára elérhetők lesznek.
* Nem minden Office 365 számítási feladattal a felhasználó földrajzi beállítás használatát támogatja.

### <a name="azure-ad-connect-support-for-synchronization"></a>A szinkronizálás Azure AD Connect-támogatás

Az Azure AD Connect szinkronizálásának támogatja a **preferredDataLocation** az attribútum **felhasználói** verziójában 1.1.524.0 objektumok és újabb verziók. Konkrétan:

* A séma az objektumtípus **felhasználói** a az Azure AD-összekötő az időtartam közé tartozik a **preferredDataLocation** attribútum. Az attribútum egyértékű karakterlánc típusú.
* A séma az objektumtípus **személy** a metaverzumban van terjeszteni a **preferredDataLocation** attribútum. Az attribútum egyértékű karakterlánc típusú.

Alapértelmezés szerint **preferredDataLocation** szinkronizálás nincs engedélyezve. Ez a szolgáltatás nagyobb szervezeteknek készült. Is meg kell adnia ahhoz, hogy a felhasználók számára az Office 365 földrajzi attribútumként, mert nincs **preferredDataLocation** a helyszíni Active Directory-attribútumban. A topológiában a minden egyes szervezet különböző.

> [!IMPORTANT]
> Azure AD lehetővé teszi a **preferredDataLocation** attribútuma **a felhő felhasználói objektumok** konfigurálandó közvetlenül az Azure AD PowerShell segítségével. Már nem az Azure AD lehetővé teszi a **preferredDataLocation** attribútuma **szinkronizált felhasználói objektumok** konfigurálandó közvetlenül az Azure AD PowerShell segítségével. Ez az attribútum konfigurálása **szinkronizált felhasználói objektumok**, az Azure AD Connect kell használnia.

A szinkronizálás engedélyezése előtt:

* Döntse el, mely a helyszíni Active Directory-attribútumot, az adatforrás-attribútum használható. A típus kötelező **egyértékű karakterlánc**. A következő lépések, egy a a **extensionAttributes** szolgál.
* Ha korábban már konfigurálta a **preferredDataLocation** attribútum meglévő **szinkronizált felhasználói objektumok** az Azure AD PowerShell segítségével az Azure AD-ben a attribútum értékeit a backport kell a megfelelő **felhasználói** objektumokat a helyszíni Active Directoryban.

    > [!IMPORTANT]
    > Ebben az esetben nem backport ezeket az értékeket az Azure AD Connect meglévő attribútumértékek eltávolítja az Azure AD-ben amikor a szinkronizálás a **preferredDataLocation** attribútum engedélyezve van.

* A forrásattribútum most a helyi Active Directory-felhasználói objektumok legalább néhány konfigurálását. Ezzel az ellenőrzéshez később.

Az alábbi szakaszok azokat a lépéseket, a szinkronizálás engedélyezése a **preferredDataLocation** attribútum.

> [!NOTE]
> A környezet az Azure AD központi telepítés egyerdős topológiával és egyéni szinkronizálási szabályok nélkül a lépéseket ismerteti. Ha egy Többerdős topológia, egyéni szinkronizálási szabályok konfigurálva, vagy egy átmeneti kiszolgálón rendelkezik, ennek megfelelően kell módosítani a lépéseket.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés: Tiltsa le a szinkronizálásütemező, és ellenőrizze, hogy nincs folyamatban lévő szinkronizálás
Elkerülése érdekében az Azure AD-exportálás alatt álló nem szándékolt módosításokat, győződjön meg arról, hogy szinkronizálás nem történik meg, amíg közepén szinkronizálási szabályok frissítése. A beépített szinkronizálásütemezési letiltása:

1. Indítsa el a PowerShell-munkamenetben az Azure AD Connect kiszolgálón.
2. Ez a parancsmag futtatásával ütemezett szinkronizálás letiltása: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Indítsa el a **Synchronization Service Managert** címen **START** > **szinkronizálási szolgáltatás**.
4. Válassza ki a **műveletek** fülre, és ellenőrizze, nincs az állapottal művelet *folyamatban*.

![Képernyőfelvétel a Synchronization Service Managert](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>2. lépés: Az adatforrás-attribútum hozzáadása a helyi Active Directory-összekötő séma
Nem minden Azure AD-attribútumok importálása a helyszíni Active Directory kapcsolódási térbe kerülnek. Ha be van jelölve, alapértelmezés szerint nem szinkronizált attribútum használatára, akkor szüksége importálnia. Az adatforrás-attribútum hozzáadása az importált attribútumok listáját:

1. Válassza ki a **összekötők** lapon a Synchronization Service Managert.
2. Kattintson a jobb gombbal a helyszíni Active Directory-összekötőt, és válassza ki **tulajdonságok**.
3. Az előugró párbeszédpanelen keresse meg a **attribútumok kiválasztása** fülre.
4. Ellenőrizze, hogy a kiválasztott használandó adatforrás-attribútum be van jelölve, a attribútum listában. Ha nem látja a attribútum, akkor jelölje be a **összes megjelenítése** jelölőnégyzetet.
5. Mentéséhez válasszon **OK**.

![Képernyőfelvétel a Synchronization Service Managert, és a Tulajdonságok párbeszédpanel](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>3. lépés: Hozzáadása **preferredDataLocation** az Azure AD-összekötő séma
Alapértelmezés szerint a **preferredDataLocation** attribútum nincs importálva a Azure AD kapcsolódási térbe kerülnek. Hozzáadása az importált attribútumok listáját:

1. Válassza ki a **összekötők** lapon a Synchronization Service Managert.
2. Kattintson a jobb gombbal az Azure AD-összekötő, és válassza ki **tulajdonságok**.
3. Az előugró párbeszédpanelen keresse meg a **attribútumok kiválasztása** fülre.
4. Válassza ki a **preferredDataLocation** attribútum a listában.
5. Mentéséhez válasszon **OK**.

![Képernyőfelvétel a Synchronization Service Managert, és a Tulajdonságok párbeszédpanel](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>4. lépés: Vonatkozó bejövő szinkronizálási szabály létrehozása
A bejövő szinkronizálási szabály lehetővé teszi, hogy az attribútum értéket a metaverzumba a helyszíni Active Directoryban a forrásattribútum haladjanak.

1. Indítsa el a **szinkronizálási szabályok szerkesztő** címen **START** > **szinkronizálási szabályok szerkesztő**.
2. Állítsa be a keresési szűrő **irány** kell **bejövő**.
3. Új bejövő szabály létrehozásához válassza **új szabály hozzáadása**.
4. Az a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például "az ad-felhasználó preferredDataLocation" |
    | Leírás | *Adjon meg egy egyéni leírást* |  |
    | Csatlakoztatott rendszer | *Válassza ki a helyszíni Active Directory-összekötő* |  |
    | Objektumtípus csatlakoztatva | **Felhasználó** |  |
    | Metaverzum-objektum típusa | **Person** |  |
    | Kapcsolat típusa | **Csatlakozás** |  |
    | Sorrend | *Válassza ki az 1 – 99 közötti szám* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt érték. |

5. Tartsa a **Scoping szűrő** üres, az összes objektumára. Szükség lehet az Azure AD Connect telepítési megfelelően tartalmazó szűrő végeznünk.
6. Lépjen a **átalakítása lapon**, és a következő átalakítási szabály végrehajtása:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    |Közvetlen | preferredDataLocation | Válassza ki az adatforrás-attribútum | Nincs bejelölve | Frissítés |

7. A bejövő forgalomra vonatkozó szabály létrehozásához válassza **Hozzáadás**.

![Bejövő szinkronizálási szabályának létrehozása képernyőképe](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>5. lépés: A kimenő szinkronizálási szabály létrehozása
A kimenő szinkronizálási szabály lehetővé teszi az attribútumérték felé haladjanak a metaverzumba, hogy a **preferredDataLocation** attribútum az Azure ad-ben:

1. Lépjen a **szinkronizálási szabályok szerkesztő**.
2. Állítsa be a keresési szűrő **irány** kell **kimenő**.
3. Válassza ki **új szabály hozzáadása**.
4. Az a **leírás** lapra, adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | ----- | ------ | --- |
    | Name (Név) | *Adjon meg egy nevet* | Például "ki az Azure AD-felhasználó preferredDataLocation" |
    | Leírás | *Adjon meg egy leírást* ||
    | Csatlakoztatott rendszer | *Válassza ki az Azure AD Connectoron* ||
    | Objektumtípus csatlakoztatva | **Felhasználó** ||
    | Metaverzum-objektum típusa | **Person** ||
    | Kapcsolat típusa | **Csatlakozás** ||
    | Sorrend | *Válassza ki az 1 – 99 közötti szám* | 1 – 99 egyéni szinkronizálási szabályok számára van fenntartva. Nem válasszon egy másik szinkronizálási szabály által használt érték. |

5. Lépjen a **Scoping szűrő** lapot, és a két záradékokkal egyetlen tartalmazó szűrő csoport hozzáadása:

    | Attribútum | Operátor | Érték |
    | --- | --- | --- |
    | sourceObjectType | EGYENLŐ | Felhasználó |
    | cloudMastered | NOTEQUAL | True (Igaz) |

    Hatókörként szűrő határozza meg, mely az Azure AD-objektumokat a kimenő szinkronizálási szabály vonatkozik. Ebben a példában a "Az AD-felhasználók identitását Out" azonos tartalmazó szűrőjének használjuk (out-of-box) OOB-szinkronizálási szabály. Megakadályozza, hogy a szinkronizálási szabály alkalmazták **felhasználói** objektumok, amelyek nem lettek szinkronizálva a helyszíni Active Directoryból. Szükség lehet az Azure AD Connect telepítési megfelelően tartalmazó szűrő végeznünk.

6. Lépjen a **átalakítása** lapot, és valósítja meg a következő átalakítási szabályt:

    | Típusa | Célattribútum | Forrás | Egyszer alkalmazása | Egyesítési típus |
    | --- | --- | --- | --- | --- |
    | Közvetlen | preferredDataLocation | preferredDataLocation | Nincs bejelölve | Frissítés |

7. Bezárás **Hozzáadás** a kimenő forgalomra vonatkozó szabály létrehozásához.

![Kimenő szinkronizálási szabály létrehozása képernyőképe](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>6. lépés: Teljes szinkronizálási ciklust futtatása
Általában a teljes szinkronizálási ciklust szükség. Ennek az az oka az Active Directory és az Azure AD-összekötő séma új attribútumok hozzáadta, és egyéni szinkronizálási szabályokat vezetett be. A módosítások ellenőrzéséhez őket az Azure AD-exportálás előtt. Az alábbi lépések segítségével a módosításokat, ellenőrizze a lépéseket egy teljes szinkronizálási ciklust alkotó manuális futtatása során.

1. Futtatás **teljes importálás** a helyszíni Active Directory-összekötőt a:

   1. Lépjen a **műveletek** lapon a Synchronization Service Managert.
   2. Kattintson a jobb gombbal a **a helyszíni Active Directory-összekötő**, és válassza ki **futtatása**.
   3. A párbeszédpanelen válassza ki a **teljes importálás**, és válassza ki **OK**.
   4. Várjon, amíg a művelet elvégzéséhez.

    > [!NOTE]
    > A helyszíni Active Directory-összekötőt a teljes importálás kihagyhatja, ha az adatforrás-attribútum már szerepel az importált attribútumok listájában. Ez azt jelenti hogy nem rendelkezett végezheti el a módosítást az ebben a cikkben 2. lépése során.

2. Futtatás **teljes importálás** az Azure AD Connectoron:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő**, és válassza ki **futtatása**.
   2. A párbeszédpanelen válassza ki a **teljes importálás**, és válassza ki **OK**.
   3. Várjon, amíg a művelet elvégzéséhez.

3. A meglévő szinkronizálási szabály módosítások **felhasználói** objektum.

   A forrásattribútum a helyszíni Active Directoryból, és **preferredDataLocation** az Azure AD-ről importálta minden megfelelő kapcsolódási térbe kerülnek. A teljes szinkronizálás lépés folytatása előtt hajtsa végre egy olyan előnézete **felhasználói** objektum a helyszíni Active Directory-összekötő területen. Az objektum, amelyet kivételezett kell rendelkeznie a forrásattribútum feltöltve. Egy sikeres előnézetet, amelynek **preferredDataLocation** kitöltve a metaverzumban jól jelzi, hogy konfigurálta a szinkronizálás szabályainak megfelelően. Előnézet módjáról további információkért lásd: [ellenőrzéséhez](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change).

4. Futtatás **teljes szinkronizálás** a helyszíni Active Directory-összekötőt a:

   1. Kattintson a jobb gombbal a **a helyszíni Active Directory-összekötő**, és válassza ki **futtatása**.
   2. A párbeszédpanelen válassza ki a **teljes szinkronizálás**, és válassza ki **OK**.
   3. Várjon, amíg a művelet elvégzéséhez.

5. Győződjön meg arról **függőben lévő Exportálásokról** az Azure AD:

   1. Kattintson a jobb gombbal a **Azure AD-összekötő**, és válassza ki **Összekötőtér keresési**.
   2. Az a **Összekötőtér keresési** párbeszédpanel:

        a. Állítsa be **hatókör** való **exportálási függőben lévő**.<br>
        b. Jelölje be mindhárom jelölőnégyzetet, beleértve a **hozzáadása, módosítása és törlése**.<br>
        c. Válassza ki, ha az exportálandó módosításokkal objektumok listáját, **keresési**. Vizsgálja meg az adott objektum a módosításokat, kattintson duplán az objektum.<br>
        d. Győződjön meg arról, hogy a változások várhatók.

6. Futtatás **exportálása** a a **Azure AD Connectoron**

   1. Kattintson a jobb gombbal a **Azure AD-összekötő**, és válassza ki **futtatása**.
   2. Az a **futtatása összekötő** párbeszédpanelen jelölje ki **exportálása**, és válassza ki **OK**.
   3. Várjon, amíg a művelet elvégzéséhez.

> [!NOTE]
> Bizonyára észrevette, hogy, hogy a lépéseket tartalmazza a teljes szinkronizálás lépés az Azure AD Connectoron, vagy az Active Directory-összekötőt a exportálását. A lépések szükségesek nem, mert az attribútum értékei vannak áramlanak a helyszíni Active Directory csak az Azure ad.

## <a name="step-7-re-enable-sync-scheduler"></a>7. lépés: Engedélyezze újra a szinkronizálásütemező
Engedélyezze újra a beépített szinkronizálásütemezési:

1. Indítson el egy PowerShell-munkamenetet.
2. Ez a parancsmag futtatásával újraengedélyezését, ütemezett szinkronizálás: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>8. lépés: Az eredmény ellenőrzése
Ellenőrizze a konfigurációt, majd engedélyezze a felhasználók számára az idő már.

1. Adja hozzá a felhasználó a kijelölt attribútum a földrajzi. A rendelkezésre álló geos listája megtalálható [ezt a táblázatot](#enable-synchronization-of-preferreddatalocation).  
![A felhasználó hozzá AD attribútum képernyőképe](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Várjon, amíg az attribútum az Azure AD-szinkronizálásának engedélyezése.
3. Exchange Online PowerShell használ, győződjön meg arról, hogy a postaláda régió helyesen van beállítva.  
![Exchange Online PowerShell képernyőképe](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Feltéve, hogy a bérlő jelölte meg kell tudni használni a szolgáltatás, a postaláda átkerül a megfelelő földrajzi. Ez a kiszolgálónév megnézi, ahol a postaláda ellenőrizheti.
4. Győződjön meg arról, hogy ez a beállítás volt hatékony sok postafiókot, használja a parancsfájl a [TechNet gallery](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Ezt a parancsfájlt is rendelkezik az összes Office 365 adatközpontok server előtagok listáját, és mely földrajzi helyen található. Használat az előző lépésben hivatkozásként való ellenőrizze a postaláda helyét.

## <a name="next-steps"></a>További lépések

További tudnivalók Multi-földrajzi az Office 365-ben:

* [Az ignite-on multi-földrajzi munkamenetek](https://aka.ms/MultiGeoIgnite)
* [Multi-földrajzi a onedrive-on](https://aka.ms/OneDriveMultiGeo)
* [A SharePoint Online multi-földrajzi](https://aka.ms/SharePointMultiGeo)

További tudnivalók a Szinkronizáló vezérlő konfigurációs modell:

* További információk a konfigurációs modell [ismertetése deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* További információk az a kifejezés nyelv [ismertetése deklaratív kiépítés kifejezések](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

Áttekintő témakör:

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
