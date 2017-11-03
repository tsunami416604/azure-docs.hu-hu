---
title: "Az Azure AD szinkronizálási szolgáltatás kezelő felhasználói felületének összekötők |} Microsoft Docs"
description: "Ismerje meg a csatlakozók fülre a Synchronization Service Managert, az Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c0fae4b1755ca95466eeffb5ce61c1c7855d7381
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Az az Azure AD Connect szinkronizálási Service Manager-összekötők használata

![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

Az összekötők lapon a szinkronizálási motor csatlakozik-e az összes rendszer kezelésére szolgál.

## <a name="connector-actions"></a>Összekötő műveletek
| Műveletek | Megjegyzés |
| --- | --- |
| Létrehozás |Ne használjon. További AD-erdővel történő kapcsolódáshoz, a telepítési varázslóval. |
| Tulajdonságok |Tartomány és szervezeti egységek szűrése használható. |
| [Törlés](#delete) |Használják, vagy törölni kívánja a kapcsolódási térbe az adatokat, vagy törölje a kapcsolatot az adott erdő. |
| [Futtatási profilok konfigurálása](#configure-run-profiles) |Tartomány kivételével szűrése mit itt konfigurálni. Ez a művelet segítségével tekintse meg a már konfigurált futtatási profilokat. |
| Futtassa a következőt: |Egyszeri futtatás profil indításához használt. |
| Leállítás |Jelenleg fut egy profil összekötő leáll. |
| Összekötő exportálása |Ne használjon. |
| Összekötő importálása |Ne használjon. |
| Frissítés összekötő |Ne használjon. |
| Séma frissítése |A gyorsítótárazott séma frissítése. Azt használata ajánlott, ha szeretné a telepítési varázsló ehelyett óta, amely is frissítések szabályok szinkronizálása. |
| [Összekötőtér keresése](#search-connector-space) |Objektumok kereséséhez használható, és [hajtsa végre az objektum és az adatokat a rendszer](#follow-an-object-and-its-data-through-the-system). |

### <a name="delete"></a>Törlés
A törlési művelet használható két különböző fogalom.  
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

A beállítás **törlése csak a kapcsolódási térbe** összes adat eltávolítható, de tartani a konfigurációt.

A beállítás **összekötő törlése és a connector** eltávolítja az adatok és a konfigurációt. Ha Csatlakozás erdőhöz már nem szeretné ezt a beállítást használják.

Mindkét lehetőség minden objektumokat szinkronizálni, és frissítése a metaverzum-objektum. Ez a művelet hosszú ideig futó művelet.

### <a name="configure-run-profiles"></a>Futtatási profilok konfigurálása
Ezzel a beállítással a futtatási profil összekötőhöz beállított láthatja.

![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Összekötőtér keresése
A keresési összekötő terület művelet célszerű található objektumok és adatok problémák elhárításához.

![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Kiválasztásával indítsa el a **hatókör**. Kereshet az adatok alapján (RDN, DN, rögzítési, részfájának), vagy az objektum (egyéb beállítások) állapot.  
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Ha például egy részfájának keresést, egy szervezeti egység összes objektum beolvasása.  
![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
A rácsban jelöljön ki egy objektumot, válassza ki **tulajdonságok**, és [követve](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) az adatforrás kapcsolódási térbe, a metaverzumba, és a cél összekötő területre.

### <a name="changing-the-ad-ds-account-password"></a>Az Active Directory tartományi szolgáltatások fiók jelszavának módosítása
Ha megváltoztatja a jelszavát, a szinkronizálási szolgáltatás már nem kell tudni importálási/exportálási módosításait a helyszíni AD.   A következő jelenhetnek meg:

- Az importálási/exportálási lépést az AD-összekötő "no-start-hitelesítő adatok" hibaüzenettel meghiúsul.
- A Windows eseménynaplóban az alkalmazások eseménynaplójában hibát tartalmaz Event ID 6000 és az üzenet "a"contoso.com"kezelőügynök futtatása sikertelen, mert a hitelesítő adatok érvénytelenek."

A probléma megoldásához frissítse az Active Directory tartományi szolgáltatások felhasználói fiókot a következő:


1. Indítsa el a Synchronization Service Managert (KEZDŐ → szinkronizálási szolgáltatás).
</br>![Szinkronizálás a Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)
2. Lépjen a **összekötők** fülre.
3. Válassza ki az AD összekötőt, amely a Tartományi fiók használatára van konfigurálva.
4. A műveletek, válassza ki a **tulajdonságok**.
5. Az előugró párbeszédpanelen kattintson a csatlakozás Active Directory erdő:
6. Az erdő neve jelzi a megfelelő helyszíni AD.
7. A felhasználónév azt jelzi, hogy a szinkronizáláshoz használt AD DS-fiókjához.
8. Adja meg az Active Directory tartományi szolgáltatások új jelszavát a jelszó szövegmezőjét ![az Azure AD Connect Sync titkosítási kulcs segédprogram](media/active-directory-aadconnectsync-encryption-key/key6.png)
9. Kattintson az OK gombra az új jelszó mentése, és indítsa újra a szinkronizálási szolgáltatást eltávolítani a régi jelszó memória-gyorsítótárból.



## <a name="next-steps"></a>Következő lépések
További információ a [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
