---
title: 'Azure AD Connect Cloud kiépítési ügynök: a beállításjegyzék beállításainak kezelése | Microsoft Docs'
description: Ez a cikk bemutatja, hogyan kezelheti a beállításjegyzék beállításait a Azure AD Connect Cloud kiépítési ügynökben.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3bd938355d138e660958e34d046d7af03e75c7
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371134"
---
# <a name="manage-agent-registry-options"></a>Ügynök beállításjegyzék-beállításainak kezelése

Ez a szakasz azokat a beállításjegyzék-beállításokat ismerteti, amelyekkel szabályozhatja az Azure AD Connect létesítési ügynök futásidejű feldolgozási viselkedését. 

## <a name="configure-ldap-connection-timeout"></a>Az LDAP-kapcsolat időtúllépésének konfigurálása
Ha a konfigurált Active Directory tartományvezérlőkön végez LDAP-műveleteket, alapértelmezés szerint a kiépítési ügynök az alapértelmezett kapcsolati időtúllépési értéket használja 30 másodpercnél. Ha a tartományvezérlő több időt vesz igénybe, a következő hibaüzenet jelenik meg az ügynök naplófájljában: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

Az LDAP-keresési műveletek hosszabb időt vehetnek igénybe, ha a keresési attribútum nincs indexelve. Első lépésként, ha a fenti hiba jelenik meg, először ellenőrizze, hogy a keresési/keresési attribútum [indexelt](https://docs.microsoft.com/windows/win32/ad/indexed-attributes)-e. Ha a keresési attribútumok indexelve vannak, és a hiba továbbra is fennáll, az alábbi lépések végrehajtásával növelheti az LDAP-kapcsolat időtúllépését: 

1. Jelentkezzen be rendszergazdaként az Azure AD Connect üzembe helyezési ügynököt futtató Windows-kiszolgálón.
1. A *Futtatás* menüpont használatával nyissa meg a beállításszerkesztőt (regedit.exe) 
1. Keresse meg a kulcs mappáját **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Kattintson a jobb gombbal, és válassza a "New-> string Value" lehetőséget.
1. Adja meg a nevet: `LdapConnectionTimeoutInMilliseconds`
1. Kattintson duplán az **érték nevére** , és adja meg az értéket `60000` ezredmásodpercben.
    > [!div class="mx-imgBorder"]
    > ![LDAP-kapcsolat időtúllépése](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Indítsa újra a Azure AD Connect kiépítési szolgáltatást a *szolgáltatások* konzolról.
1. Ha több kiépítési ügynököt telepített, alkalmazza a beállításjegyzék módosítását az összes ügynökre a konzisztencia érdekében. 

## <a name="configure-referral-chasing"></a>Hivatkozói átirányítás konfigurálása
Alapértelmezés szerint az Azure AD Connect kiépítési ügynök nem üldözi az [átirányítást](https://docs.microsoft.com/windows/win32/ad/referrals). Előfordulhat, hogy engedélyezni szeretné az átirányítást, hogy támogassa bizonyos HR bejövő kiépítési forgatókönyveket, például a következőket: 
* UPN egyediségének ellenőrzése több tartományon belül
* A tartományok közötti kezelőre vonatkozó hivatkozások feloldása

Az alábbi lépéseket követve bekapcsolhatja az átirányítást:

1. Jelentkezzen be rendszergazdaként az Azure AD Connect üzembe helyezési ügynököt futtató Windows-kiszolgálón.
1. A *Futtatás* menüpont használatával nyissa meg a beállításszerkesztőt (regedit.exe) 
1. Keresse meg a kulcs mappáját **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Kattintson a jobb gombbal, és válassza a "New-> string Value" lehetőséget.
1. Adja meg a nevet: `ReferralChasingOptions`
1. Kattintson duplán az **érték nevére** , és adja meg a Value (érték `96` ) értéket. Ez az érték felel meg a konstans értékének, `ReferralChasingOptions.All` és megadja, hogy a rendszer a részfa és az alapszintű hivatkozásokat is követi az ügynök. 
    > [!div class="mx-imgBorder"]
    > ![Hivatkozó átirányítás](media/how-to-manage-registry-options/referral-chasing.png)
1. Indítsa újra a Azure AD Connect kiépítési szolgáltatást a *szolgáltatások* konzolról.
1. Ha több kiépítési ügynököt telepített, alkalmazza a beállításjegyzék módosítását az összes ügynökre a konzisztencia érdekében.

> [!NOTE]
> A beállításjegyzék beállításait a [részletes naplózás](how-to-troubleshoot.md#log-files)engedélyezésével ellenőrizheti. Az ügynök indításakor kibocsátott naplók megjelenítik a beállításjegyzékből kiválasztott konfigurációs értékeket. 

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)

