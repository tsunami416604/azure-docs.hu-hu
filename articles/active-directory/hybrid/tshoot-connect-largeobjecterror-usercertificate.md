---
title: Azure AD Connect – LargeObject hibák, amelyeket a userCertificate attribútum okoz | Microsoft dokumentumok
description: Ez a témakör a userCertificate attribútum által okozott LargeObject hibák javítási lépéseit ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c851b5ef024e6584e6f8c93995208b08a91fbb60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "62095489"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect szinkronizálás: A userCertificate attribútum által okozott LargeObject-hibák kezelése

Az Azure AD a **userCertificate** attribútumlegfeljebb **15** tanúsítványértéket kényszerít ki. Ha az Azure AD Connect 15-nél több értéket tartalmazó objektumot exportál az Azure AD-be, az Azure AD **egy LargeObject hibát** ad vissza a következő üzenettel:

>*"A kiosztott objektum túl nagy. Vágja le az objektum attribútumértékeinek számát. A műveletet a rendszer a következő szinkronizálási ciklusban újra megkísérli..."*

A LargeObject hibát más AD-attribútumok is okozhatták. Annak ellenőrzéséhez, hogy valóban a userCertificate attribútum okozza, ellenőriznie kell az objektumot a helyszíni AD-ben vagy a [Szinkronizálási szolgáltatáskezelő metaverzum-keresésében.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch)

A bérlőben lévő objektumok LargeObject-hibákkal való beszerzéséhez használja az alábbi módszerek egyikét:

 * Ha a bérlő engedélyezve van az Azure AD Connect Health szinkronizálásához, tekintse meg a [szinkronizálási hibajelentés](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync) biztosított.
 
 * Az egyes szinkronizálási ciklusok végén küldött címtár-szinkronizálási hibák értesítési e-mail-címe tartalmazza a LargeObject hibákkal rendelkező objektumok listáját. 
 * A [Szinkronizálási szolgáltatáskezelő műveletek lap](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) megjeleníti a LargeObject-hibákkal rendelkező objektumok listáját, ha a legújabb Exportálás az Azure AD-be műveletre kattint.
 
## <a name="mitigation-options"></a>Kockázatcsökkentési lehetőségek
Amíg a LargeObject hiba nem oldódik meg, az ugyanazon objektum hoz a többi attribútummódosítása nem exportálható az Azure AD-be. A hiba elhárításához a következő lehetőségeket veheti figyelembe:

 * Frissítse az Azure AD Connectet az 1.1.524.0-s vagy azt követő buildeléséhez. Az Azure AD Connect 1.1.524.0-s buildjében a beépített szinkronizálási szabályok frissültek, hogy ne exportálják a userCertificate és userSMIMECertificate attribútumokat, ha az attribútumok 15-nél több értékkel rendelkeznek. Az Azure AD Connect frissítéséről az [Azure AD Connect: Frissítés egy korábbi verzióról a legújabbra](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)című cikkben olvashat részletesen.

 * Valósítson meg egy **kimenő szinkronizálási szabályt** az Azure AD Connectben, amely null értéket exportál **a 15-nél több tanúsítványértékkel rendelkező objektumok tényleges értékei helyett.** Ez a beállítás akkor megfelelő, ha nem szükséges a tanúsítvány értékek et az Azure AD-be a 15-nél több értéket igénylő objektumok esetében. A szinkronizálási szabály megvalósításáról a következő Szakasz [A userCertificate attribútum exportálásának korlátozását célzó Szinkronizálási szabály megvalósítása](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute)című szakaszban talál további részleteket.

 * Csökkentse a tanúsítványértékek számát a helyszíni AD objektumon (15 vagy kevesebb) a szervezet által már nem használt értékek eltávolításával. Ez akkor megfelelő, ha az attribútum felfújni a lejárt vagy nem használt tanúsítványok okozzák. Az itt [elérhető PowerShell-parancsfájl](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) segítségével megkeresheti, biztonsági másolatot készíthet és törölheti a lejárt tanúsítványokat a helyszíni AD-ben. A tanúsítványok törlése előtt ajánlott a szervezet nyilvános kulcsú infrastruktúrájának rendszergazdáival egyezgetni.

 * Konfigurálja az Azure AD Connectet úgy, hogy kizárja a userCertificate attribútumot az Azure AD-be való exportálásból. Általában nem javasoljuk ezt a beállítást, mivel az attribútumot a Microsoft Online Services bizonyos esetek engedélyezésére is használhatják. Elsősorban:
    * A User objektum userCertificate attribútumát az Exchange Online és az Outlook ügyfelek üzenetaláírásra és titkosításra használják. A funkcióról az [Üzenetek aláírásáról és titkosításáról az S/MIME](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx)című cikkben olvashat bővebben.

    * A computer objektum userCertificate attribútumát az Azure AD arra használja, hogy a Windows 10 helyszíni tartományhoz csatlakozó eszközei csatlakozzanak az Azure AD-hez. Ha többet szeretne megtudni erről a funkcióról, olvassa el a [tartományhoz csatlakozó eszközök csatlakoztatása az Azure AD for Windows 10-es élményeket című cikket.](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy)

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Szinkronizálási szabály megvalósítása a userCertificate attribútum exportálásának korlátozására
A userCertificate attribútum által okozott LargeObject-hiba megoldásához valósíthat meg egy kimenő szinkronizálási szabályt az Azure AD Connect ben, amely null értéket exportál **a 15-nél több tanúsítványértékkel rendelkező objektumok tényleges értékei helyett.** Ez a szakasz a **felhasználói** objektumok szinkronizálási szabályának megvalósításához szükséges lépéseket ismerteti. A lépések a **Névjegy** és a **Számítógép** objektumokhoz igazíthatók.

> [!IMPORTANT]
> Null érték exportálása eltávolítja a korábban exportált tanúsítványértékeket az Azure AD-be.

A lépések a következőképpen foglalhatók össze:

1. Tiltsa le a szinkronizálási ütemezőt, és ellenőrizze, hogy nincs-e folyamatban szinkronizálás.
3. Keresse meg a userCertificate attribútum meglévő kimenő szinkronizálási szabályát.
4. Hozza létre a szükséges kimenő szinkronizálási szabályt.
5. Ellenőrizze az új szinkronizálási szabályt egy LargeObject hibával rendelkező meglévő objektumon.
6. Alkalmazza az új szinkronizálási szabályt a LargeObject hibával rendelkező fennmaradó objektumokra.
7. Ellenőrizze, hogy nincsenek váratlan módosítások, amelyek az Azure AD-be való exportálásra várnak.
8. Exportálja a módosításokat az Azure AD-be.
9. A szinkronizálási ütemező újbóli engedélyezése.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés A szinkronizálási ütemező letiltása és annak ellenőrzése, hogy nincs-e folyamatban szinkronizálás
Győződjön meg arról, hogy nem történik szinkronizálás, miközben egy új szinkronizálási szabály megvalósítása közben kerül sor, hogy elkerülje a nem kívánt módosítások exportálását az Azure AD-be. A beépített szinkronizálási ütemező letiltása:
1. Indítsa el a PowerShell-munkamenetet az Azure AD Connect-kiszolgálón.

2. Az ütemezett szinkronizálás letiltása parancsmag futtatásával:`Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Az előző lépések csak az Azure AD Connect és a beépített ütemező újabb verzióira (1.1.xxx.x) vonatkoznak. Ha az Azure AD Connect régebbi verzióit (1.0.xxx.x) használja, amely Windows Feladatütemezőt használ, vagy saját egyéni ütemezőjét (nem gyakori) használja a rendszeres szinkronizálás elindításához, ennek megfelelően le kell tiltania őket.

1. Indítsa el a **szinkronizálási szolgáltatáskezelőt** a START → Szinkronizálási szolgáltatás sal.

1. Lépjen a **Műveletek** lapra, és ellenőrizze, hogy nincs-e olyan művelet, amelynek állapota *"folyamatban van".*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>2. lépés A userCertificate attribútum meglévő kimenő szinkronizálási szabályának megkeresése
Léteznie kell egy meglévő szinkronizálási szabály, amely engedélyezve van, és konfigurálva van a usercertificate attribútum exportálására a felhasználói objektumok hoz az Azure AD-be. Keresse meg ezt a szinkronizálási szabályt, hogy megtudja, **mia sorrendje** és **hatókörének szűrőkonfigurációja:**

1. Indítsa el a **Szinkronizálási szabályok szerkesztőjét** a START → Szinkronizálási szabályok szerkesztőjével.

2. Konfigurálja a keresési szűrőket a következő értékekkel:

    | Attribútum | Érték |
    | --- | --- |
    | Irány |**Kimenő** |
    | MV objektumtípus |**Személy** |
    | Összekötő |*Az Azure AD-összekötő neve* |
    | Összekötő objektum típusa |**Felhasználó** |
    | MV attribútum |**userCertificate** |

3. Ha OOB (out-of-box) szinkronizálási szabályokat használ az Azure AD-összekötővel a felhasználói objektumok userCertficiate attribútumának exportálásához, vissza kell kapnia a *"Out to AAD – User ExchangeOnline"* szabályt.
4. Jegyezze fel a szinkronizálási szabály **elsőbbségi** értékét.
5. Jelölje ki a szinkronizálási szabályt, és kattintson a **Szerkesztés gombra.**
6. A *"Fenntartott szabály megerősítésének szerkesztése"* előugró párbeszédpanelen kattintson a **Nem**gombra. (Ne aggódjon, nem fogunk változtatni ezen a szinkronizálási szabályon).
7. A szerkesztési képernyőn válassza a **Hatókörszűrő** lapot.
8. Jegyezze fel a hatókörszűrő konfigurációját. Ha az OOB szinkronizálási szabályt használja, pontosan **egy hatókörszűrő-csoportnak**kell lennie, amely két záradékot tartalmaz, beleértve a következőket:

    | Attribútum | Művelet | Érték |
    | --- | --- | --- |
    | sourceObjectType | Egyenlő | Felhasználó |
    | cloudMastered | NEM EGYENLŐ | True (Igaz) |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>3. lépés A szükséges kimenő szinkronizálási szabály létrehozása
Az új szinkronizálási szabálynak ugyanazokkal a **hatókörszűrővel** és magasabb prioritással kell **rendelkeznie,** mint a meglévő szinkronizálási szabálynak. Ez biztosítja, hogy az új szinkronizálási szabály ugyanazokra az objektumokra vonatkozzon, mint a meglévő szinkronizálási szabály, és felülbírálja a userCertificate attribútum meglévő szinkronizálási szabályát. A szinkronizálási szabály létrehozása:
1. A Szinkronizálási szabályok szerkesztőben kattintson az **Új szabály hozzáadása** gombra.
2. A **Leírás lapon**adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Név | *Adjon nevet* | Pl.: *"Out to AAD – Custom override for userCertificate"* |
    | Leírás | *Leírás megadása* | *Például: "Ha a userCertificate attribútum 15-nél több értékből áll, exportálja a NULL értéket."* |
    | Csatlakoztatott rendszer | *Válassza ki az Azure AD-összekötőt* |
    | Csatlakoztatott rendszerobjektum-típus | **Felhasználó** | |
    | Metaverzum objektum típusa | **Személy** | |
    | Hivatkozás típusa | **Csatlakozás** | |
    | Precedencia | *Válasszon egy számot 1 - 99 között* | A kiválasztott számot egyetlen meglévő szinkronizálási szabály sem használhatja, és a meglévő szinkronizálási szabálynál alacsonyabb (és ezért magasabb prioritású) értékkel rendelkezik. |

3. Nyissa meg a **Hatókörszűrő** lapot, és valósítsa meg ugyanazt a hatókörszűrőt, amelyet a meglévő szinkronizálási szabály használ.
4. A **Csatlakozás szabályok** lap kihagyása
5. Az **Átalakítások** lapon új átalakítást adhat hozzá a következő konfigurációhasználatával:

    | Attribútum | Érték |
    | --- | --- |
    | Tördelés típusa |**Kifejezés** |
    | Célattribútum |**userCertificate** |
    | Forrás attribútuma |*Használja a következő kifejezést:*`IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. A **szinkronizálási** szabály létrehozásához kattintson a Hozzáadás gombra.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>4. lépés Az új szinkronizálási szabály ellenőrzése egy LargeObject hibával rendelkező meglévő objektumon
Ez annak ellenőrzésére szolgál, hogy a létrehozott szinkronizálási szabály megfelelően működik-e egy LargeObject hibával rendelkező meglévő AD objektumon, mielőtt más objektumokra alkalmazna:
1. Nyissa meg a Szinkronizálási szolgáltatáskezelő **Műveletek** lapját.
2. Válassza ki a legutóbbi Exportálás az Azure AD-be műveletet, és kattintson az egyik olyan objektumra, amely largeobject hibákat tartalmaz.
3.  Az Összekötő térobjektum tulajdonságai előugró képernyőn kattintson az **Előnézet** gombra.
4. Az Előnézet előugró képernyőn válassza a **Teljes szinkronizálás** lehetőséget, majd kattintson az **Előnézet véglegesítése gombra.**
5. Zárja be az Előnézet és az Összekötő térobjektum tulajdonságai képernyőt.
6. Nyissa meg az **Összekötők** lapot a Szinkronizálási szolgáltatáskezelőben.
7. Kattintson a jobb gombbal az **Azure AD-összekötőre,** és válassza a **Futtatás...**
8. A Run Connector előugró ablakban válassza az **Exportálás** lépést, majd kattintson az **OK**gombra.
9. Várja meg, amíg az Exportálás az Azure AD-be befejeződik, és ellenőrizze, hogy nincs-e több LargeObject hiba az adott objektumon.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>5. lépés Az új szinkronizálási szabály alkalmazása a LargeObject hibával rendelkező fennmaradó objektumokra
A szinkronizálási szabály hozzáadása után egy teljes szinkronizálási lépést kell futtatnia az AD-összekötőn:
1. Nyissa meg az **Összekötők** lapot a Szinkronizálási szolgáltatáskezelőben.
2. Kattintson a jobb gombbal az **AD-összekötőre,** és válassza a **Futtatás...**
3. A Run Connector előugró ablakban válassza a **Teljes szinkronizálás** lépés lehetőséget, és kattintson az **OK**gombra.
4. Várja meg, amíg a teljes szinkronizálási lépés befejeződik.
5. Ha egynél több AD-összekötővel rendelkezik, ismételje meg a fenti lépéseket a fennmaradó AD-összekötők esetében. Általában több összekötők szükségesek, ha több helyszíni könyvtárak.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>6. lépés Ellenőrizze, hogy nincsenek-e váratlan módosítások az Azure AD-be való exportálásra
1. Nyissa meg az **Összekötők** lapot a Szinkronizálási szolgáltatáskezelőben.
2. Kattintson a jobb gombbal az Azure AD-összekötőre, és válassza a **Keresési összekötő tér ben parancsot.** **Azure AD**
3. A Keresésösszekötő tér előugró ablakában:
    1. A Hatókör beállítása **függőexportálásra.**
    2. Jelölje be mind a 3 jelölőnégyzetet, beleértve a **Hozzáadás,** **a Módosítás** és a **Törlés jelölőnégyzetet.**
    3. Kattintson a **Keresés** gombra az Azure AD-be exportra váró összes objektum visszaadásához.
    4. Ellenőrizze, hogy nincsenek-e váratlan módosítások. Egy adott objektum módosításának vizsgálatához kattintson duplán az objektumra.

### <a name="step-7-export-the-changes-to-azure-ad"></a>7. lépés A módosítások exportálása az Azure AD-be
A módosítások exportálása az Azure AD-be:
1. Nyissa meg az **Összekötők** lapot a Szinkronizálási szolgáltatáskezelőben.
2. Kattintson a jobb gombbal az **Azure AD-összekötőre,** és válassza a **Futtatás...**
4. A Run Connector előugró ablakban válassza az **Exportálás** lépést, majd kattintson az **OK**gombra.
5. Várja meg, amíg az Exportálás az Azure AD-be befejeződik, és ellenőrizze, hogy nincs-e több LargeObject hiba.

### <a name="step-8-re-enable-sync-scheduler"></a>8. lépés Szinkronizálási ütemező újbóli engedélyezése
Most, hogy a probléma megoldódott, engedélyezze újra a beépített szinkronizálási ütemezőt:
1. Indítsa el a PowerShell-munkamenetet.
2. Az ütemezett szinkronizálás újbóli engedélyezése parancsmag futtatásával:`Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Az előző lépések csak az Azure AD Connect és a beépített ütemező újabb verzióira (1.1.xxx.x) vonatkoznak. Ha az Azure AD Connect régebbi verzióit (1.0.xxx.x) használja, amely Windows Feladatütemezőt használ, vagy saját egyéni ütemezőjét (nem gyakori) használja a rendszeres szinkronizálás elindításához, ennek megfelelően le kell tiltania őket.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

