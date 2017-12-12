---
title: "Az Azure AD Connect: Frissítés az SSL-tanúsítványt az Active Directory összevonási szolgáltatások (AD FS) farm |} Microsoft Docs"
description: "Ez a dokumentum adatokat a lépéseket az Azure AD Connect használatával az SSL-tanúsítvány egy AD FS-farm frissítéséhez."
services: active-directory
keywords: "az Azure ad connect, az AD FS ssl-frissítés, az AD FS-tanúsítványt frissítés, módosítsa az AD FS-tanúsítványt, új AD FS-tanúsítványt, az AD FS tanúsítványt, a frissítés AD FS ssl-tanúsítványt, a frissítés ssl tanúsítvány adfs konfigurálása az AD FS ssl-tanúsítvány, az AD FS, ssl, tanúsítvány, az AD FS szolgáltatás közötti kommunikációs tanúsítványt, a frissítés összevonási, összevonás konfigurálása, aad-csatlakozás"
authors: anandyadavmsft
manager: mtillman
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: anandy
ms.openlocfilehash: 2bd3d537761ec78ebcaab722e7928a88e90498d8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Az SSL-tanúsítványt az Active Directory összevonási szolgáltatások (AD FS) farm frissítése

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti, hogyan használhatja az Azure AD Connect az SSL-tanúsítványt az Active Directory összevonási szolgáltatások (AD FS) farm frissítéséhez. Az Azure AD Connect eszköz használatával egyszerűen frissíthetők az SSL-tanúsítványt az AD FS-farm, még akkor is, ha a felhasználó bejelentkezési módszer kijelölt nincs Active Directory összevonási szolgáltatások.

A teljes műveletet SSL-tanúsítványt az AD FS-farm összes összevonási és három egyszerű lépését a webalkalmazás-proxykiszolgálóként (WAP) kiszolgálók frissítésének végezheti el:

![Három lépést](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Az AD FS által használt tanúsítványok kapcsolatos további információkért lásd: [AD FS által használt tanúsítványok ismertetése](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Előfeltételek

* **AD FS-Farm**: Győződjön meg arról, hogy az AD FS farm Windows Server 2012 R2-alapú vagy újabb.
* **Az Azure AD Connect**: Győződjön meg arról, hogy az Azure AD Connect verziószáma 1.1.553.0 vagy újabb verzióját. A feladat fogjuk **frissítés az AD FS SSL-tanúsítvány**.

![SSL-tevékenység frissítése](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>1. lépés: Az AD FS farm adatok megadása

Az Azure AD Connect megkísérli automatikusan megjelenítheti az AD FS-farm adatait:
1. Lekérdezi a farm-adatokat az Active Directory összevonási szolgáltatások (Windows Server 2016 vagy újabb).
2. Hivatkozik az adatokat a korábbi fut, amely helyileg vannak tárolva az Azure AD Connect.

Módosíthatja a kiszolgálók hozzáadásával vagy eltávolításával a kiszolgálókat, az AD FS-farm aktuális konfigurációja megfelelően megjelenített listáját. Amint a kiszolgáló információt, az Azure AD Connect a kapcsolódási és SSL-tanúsítvány aktuális állapotát jeleníti meg.

![AD FS-kiszolgáló adatai](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Ha a lista, amely már nem része az AD FS-farm kiszolgálót tartalmaz, kattintson a **eltávolítása** törölni a kiszolgálót az AD DS farmban lévő kiszolgálók a listából.

![Kapcsolat nélküli server listában](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Egy kiszolgáló eltávolítása a listából egy AD FS-farm, az Azure AD Connectben kiszolgálóinak helyi művelet, és frissíti az AD FS farmon, az Azure AD Connect helyileg tárolja az adatait. Az Azure AD Connect nem módosítja az AD FS-t a változásnak-konfigurációt.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>2. lépés:, Adjon meg egy új SSL-tanúsítvány

Az információkat az AD FS farm kiszolgálói megerősítését követően az Azure AD Connect az új SSL-tanúsítványt kér. Adja meg a jelszóval védett PFX tanúsítványt, a telepítés folytatásához.

![SSL-tanúsítvány](./media/active-directory-aadconnectfed-ssl-update/certificate.png)

Miután megadta a tanúsítványt, az Azure AD Connect végighalad az Előfeltételek sorozata. Ellenőrizze a tanúsítvány győződjön meg arról, hogy a tanúsítvány megfelelő-e az AD FS-farm számára:

-   A tanúsítvány tulajdonos neve vagy másodlagos tulajdonosneve megegyezik vagy az összevonási szolgáltatás nevét, vagy helyettesítő tanúsítványt.
-   A tanúsítvány érvénytelen, több mint 30 napig.
-   Érvénytelen a tanúsítvány megbízhatósági láncában.
-   A tanúsítvány jelszóval védve.

## <a name="step-3-select-servers-for-the-update"></a>3. lépés: Válassza a kiszolgálók frissítése

A következő lépésben válassza ki a kiszolgálókat, amelyeket frissíteni az SSL-tanúsítvány szükséges. A frissítés nem állítható be a kiszolgálók, az offline.

![Válassza ki a kiszolgálók frissítése](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

A konfiguráció befejezése után az Azure AD Connect megjeleníti az üzenetet, amely azt jelzi, a frissítés állapotának és az AD FS-bejelentkezés ellenőrzése lehetőséget biztosít.

![A konfigurálás befejeződött](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Gyakori kérdések

* **Mi legyen az új AD FS SSL-tanúsítvány a tanúsítvány tulajdonosának neve?**

    Az Azure AD Connect ellenőrzi, hogy a tanúsítvány tulajdonos neve vagy másodlagos tulajdonosának neve tartalmazza-e az összevonási szolgáltatás nevét. Például ha az összevonási szolgáltatás nevének fs.contoso.com, a tulajdonos neve vagy másodlagos tulajdonosnév kell lennie: fs.contoso.com.  Helyettesítő tanúsítványok is használhatók.

* **Miért kérdezi meg az alkalmazás hitelesítő adatainak újra a WAP-kiszolgáló lapon?**

    Ha a hitelesítő adatokat ad meg az AD FS-kiszolgáló csatlakozik a is nincs jogosultság a kezelheti a WAP-kiszolgálókkal, majd az Azure AD Connect hitelesítő adatokat kér, amely rendszergazdai jogosultságokkal rendelkezzen a WAP-kiszolgálókkal.

* **A kiszolgáló offline állapotúként jelenik meg. Mit tegyek?**

    Az Azure AD Connect nem hajtható végre semmilyen műveletet, ha a kiszolgáló offline állapotban. Ha a kiszolgáló az AD FS-farm része, akkor ellenőrizze a kapcsolatot a kiszolgálóval. Után már megoldotta a problémát, nyomja meg a frissítési ikonra a varázslóban állapotának frissítése. Ha a kiszolgáló nem része a farm korábban, de most már nem létezik, kattintson a **eltávolítása** törölje azon kiszolgálók listájáról, hogy az Azure AD Connect tart fenn. A kiszolgáló eltávolítása a listából az Azure AD Connectben nem módosítható az AD FS konfigurációt magát. Ha az AD FS a Windows Server 2016 vagy újabb, illetve a kiszolgáló marad, a konfigurációs beállításokat használ, és a következő alkalommal újra megjelenik a feladat fut.

* **Frissíthetem a webfarm-kiszolgálók telepítése egy részét az új SSL-tanúsítvány?**

    Igen. A feladat mindig futtatható **SSL-tanúsítvány frissítése** újra a többi kiszolgálóját frissítése. A a **válassza kiszolgálók SSL tanúsítvány frissítés** lapon rendezheti a kiszolgálók listáját **SSL lejárati dátum** a még nem frissített kiszolgáló könnyen eléréséhez.

* **A kiszolgáló eltávolított az előző futtatása, de továbbra is alatt megjelenő, a kapcsolat nélküli és a felsorolt az AD FS-kiszolgálók lapon. Miért van még a kapcsolat nélküli kiszolgáló, az eltávolított után is?**

    A kiszolgáló eltávolítása a listából az Azure AD Connectben nem távolítható el a az AD FS konfigurációt. Az Azure AD Connect hivatkozik az Active Directory összevonási szolgáltatások (Windows Server 2016 vagy újabb) a farm összes információt. Ha a kiszolgáló továbbra is megtalálhatók az AD FS konfigurációt, az jelenik vissza a listában.  

## <a name="next-steps"></a>Következő lépések

- [Azure AD Connect és összevonás](active-directory-aadconnectfed-whatis.md)
- [Active Directory összevonási szolgáltatások kezelése és testreszabása az Azure AD Connecttel](active-directory-aadconnect-federation-management.md)
