---
title: Azure AD Connect – Az AD FS-farm TLS/SSL-tanúsítványának frissítése | Microsoft dokumentumok
description: Ez a dokumentum részletezi az AD FS-farm TLS/SSL-tanúsítványának az Azure AD Connect használatával történő frissítésének lépéseit.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cc768162d98402fe52b52b2826a9dbf2840a581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331742"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Active Directory összevonási szolgáltatások (AD FS) farm TLS/SSL-tanúsítványának frissítése

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogy az Azure AD Connect használatával hogyan frissítheti az Active Directory összevonási szolgáltatások (AD FS) farmJának TLS/SSL-tanúsítványát. Az Azure AD Connect eszközzel egyszerűen frissítheti a TLS/SSL tanúsítványt az AD FS-farmhoz, még akkor is, ha a kiválasztott felhasználói bejelentkezési módszer nem AD FS.

Az AD FS farm TLS/SSL-tanúsítványának frissítése teljes műveletet az összes összevonási és webalkalmazás-proxy (WAP) kiszolgálón három egyszerű lépésben hajthatja végre:

![Három lépés](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Az AD FS által használt tanúsítványokról az [AD FS által használt tanúsítványok ismertetése című](https://technet.microsoft.com/library/cc730660.aspx)témakörben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek

* **AD FS farm:** Győződjön meg arról, hogy az AD FS-farm Windows Server 2012 R2 vagy újabb rendszerű.
* **Azure AD Connect:** Győződjön meg arról, hogy az Azure AD Connect verziója 1.1.553.0 vagy újabb. Az **AD FS SSL-tanúsítvány frissítése**című feladatot fogja használni.

![TLS-feladat frissítése](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>1. lépés: Az AD FS-farm adatainak biztosítása

Az Azure AD Connect az AD FS-farmra vonatkozó információk automatikus beszerzésére törekszik:
1. A farm adatainak lekérdezése az AD FS rendszerből (Windows Server 2016 vagy újabb).
2. Az azure AD Connect helyileg tárolt információk hivatkozása.

A kiszolgálók hozzáadásával vagy eltávolításával módosíthatja a megjelenített kiszolgálók listáját, hogy azok tükrözzék az AD FS farm aktuális konfigurációját. Amint a kiszolgáló adatai megvannak adva, az Azure AD Connect megjeleníti a kapcsolatot és az aktuális TLS/SSL tanúsítvány állapotát.

![AD FS-kiszolgáló adatai](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Ha a lista olyan kiszolgálót tartalmaz, amely már nem része az AD FS-farmnak, kattintson az **Eltávolítás** gombra, ha törölni szeretné a kiszolgálót az AD FS-farm kiszolgálóinak listájából.

![Kapcsolat nélküli kiszolgáló a listában](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> A kiszolgáló eltávolítása az Azure AD Connect egy AD FS-farm kiszolgálóinak listájából egy helyi művelet, és frissíti az Azure AD Connect által helyileg karbantartott AD FS-farm adatait. Az Azure AD Connect nem módosítja az AD FS konfigurációját, hogy tükrözze a változást.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>2. lépés: Új TLS/SSL tanúsítvány biztosítása

Miután megerősítette az AD FS-farmkiszolgálókkal kapcsolatos információkat, az Azure AD Connect kéri az új TLS/SSL tanúsítványt. A telepítés folytatásához adjon meg egy jelszóval védett PFX-tanúsítványt.

![TLS/SSL tanúsítvány](./media/how-to-connect-fed-ssl-update/certificate.png)

A tanúsítvány biztosítása után az Azure AD Connect egy sor előfeltételen megy keresztül. Ellenőrizze a tanúsítványt, hogy a tanúsítvány megfelelő-e az AD FS-farmhoz:

-   A tanúsítvány tulajdonosneve/másodlagos tulajdonosneve megegyezik az összevonási szolgáltatás nevével, vagy helyettesítő tanúsítvány.
-   A tanúsítvány több mint 30 napig érvényes.
-   A megbízható tanúsítvány lánca érvényes.
-   A tanúsítvány jelszóval védett.

## <a name="step-3-select-servers-for-the-update"></a>3. lépés: A frissítés kiszolgálóinak kiválasztása

A következő lépésben válassza ki azokat a kiszolgálókat, amelyeknek frissíteniük kell a TLS/SSL tanúsítványt. Az offline kiszolgálók nem választhatók ki a frissítéshez.

![A frissítandó kiszolgálók kiválasztása](./media/how-to-connect-fed-ssl-update/selectservers.png)

A konfiguráció befejezése után az Azure AD Connect megjeleníti az üzenetet, amely jelzi a frissítés állapotát, és lehetőséget biztosít az AD FS bejelentkezés ellenőrzésére.

![A konfigurálás befejeződött](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Gyakori kérdések

* **Mi legyen az új AD FS TLS/SSL tanúsítvány tulajdonosneve?**

    Az Azure AD Connect ellenőrzi, hogy a tanúsítvány tulajdonosneve/másodlagos tulajdonosneve tartalmazza-e az összevonási szolgáltatás nevét. Ha például az összevonási szolgáltatás neve fs.contoso.com, a tulajdonos nevét/helyettes tulajdonosnevét fs.contoso.com kell.  Helyettesítő tanúsítványok is elfogadottak.

* **Miért kell ismét hitelesítő adatokat kérnem a WAP-kiszolgáló lapján?**

    Ha az AD FS-kiszolgálókhoz való csatlakozáshoz megadott hitelesítő adatok nem rendelkeznek a WAP-kiszolgálók kezeléséhez szükséges jogosultsággal, majd az Azure AD Connect olyan hitelesítő adatokat kér, amelyek rendszergazdai jogosultsággal rendelkeznek a WAP-kiszolgálókon.

* **A kiszolgáló offline állapotban jelenik meg. Mit kell tennem?**

    Az Azure AD Connect nem hajtvégre semmilyen műveletet, ha a kiszolgáló offline állapotban van. Ha a kiszolgáló az AD FS-farm része, ellenőrizze a kiszolgálóhoz való kapcsolódást. Miután megoldotta a problémát, nyomja meg a frissítés ikont a varázsló állapotának frissítéséhez. Ha a kiszolgáló korábban a farm része volt, de már nem létezik, kattintson az **Eltávolítás** gombra, ha törli az Azure AD Connect által karbantartott kiszolgálók listájáról. A kiszolgáló eltávolítása a listából az Azure AD Connect nem módosítja az AD FS konfigurációját. Ha Az AD FS-t Windows Server 2016-ban vagy újabb verzióban használja, a kiszolgáló a konfigurációs beállításokban marad, és a feladat következő futtatásakor ismét megjelenik.

* **Frissíthetem a farmkiszolgálók egy részhalmazát az új TLS/SSL tanúsítvánnyal?**

    Igen. A többi kiszolgáló frissítéséhez bármikor futtathatja ismét az **SSL-tanúsítvány frissítése** feladatot. Az **SSL-tanúsítványfrissítési kiszolgálók kiválasztása** lapon rendezheti az **SSL lejárati dátumán** lévő kiszolgálók listáját, így könnyen elérheti a még nem frissített kiszolgálókat.

* **Eltávolítottam a szervert az előző futtatásban, de még mindig offline állapotban jelenik meg, és az AD FS-kiszolgálók lapon szerepel. Miért van az offline kiszolgáló még mindig ott után is eltávolította?**

    A kiszolgáló eltávolítása a listából az Azure AD Connect nem távolítja el az AD FS konfigurációban. Az Azure AD Connect az AD FS (Windows Server 2016 vagy újabb) elemre hivatkozik a farmra vonatkozó információkért. Ha a kiszolgáló még mindig megtalálható az AD FS konfigurációban, akkor az visszakerül a listába.  

## <a name="next-steps"></a>További lépések

- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
- [Az Active Directory összevonási szolgáltatások kezelése és testreszabása az Azure AD Connect segítségével](how-to-connect-fed-management.md)

