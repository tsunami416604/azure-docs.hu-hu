---
title: 'Azure Active Directory Domain Services: Hálózati biztonsági csoportok – problémamegoldás | Microsoft Docs'
description: A hálózati biztonsági csoport konfigurációjának hibaelhárítása Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 450ee5635b378ed7c4d4e4bedc1c4245f6b52d70
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "70743444"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>A felügyelt tartomány érvénytelen hálózati konfigurációjának hibáinak megoldása
Ez a cikk segítséget nyújt a hálózattal kapcsolatos olyan konfigurációs hibák elhárításához és megoldásához, amelyek a következő riasztási üzenetet eredményezik:

## <a name="alert-aadds104-network-error"></a>Riasztás AADDS104: Hálózati hiba történt
**Riasztási üzenet:** *A Microsoft nem tudja elérni a tartományvezérlőket ehhez a felügyelt tartományhoz. Ez akkor fordulhat elő, ha a virtuális hálózaton konfigurált hálózati biztonsági csoport (NSG) blokkolja a hozzáférést a felügyelt tartományhoz. Egy másik lehetséges ok, ha van egy felhasználó által megadott útvonal, amely blokkolja az internetről érkező bejövő forgalmat.*

A hálózati hibák leggyakoribb okai a Azure AD Domain Services esetében érvénytelenek a NSG-konfigurációk. A virtuális hálózathoz konfigurált hálózati biztonsági csoportnak (NSG) engedélyeznie kell [bizonyos portok](network-considerations.md#network-security-groups-and-required-ports)elérését. Ha ezek a portok le vannak tiltva, a Microsoft nem tudja figyelni vagy frissíteni a felügyelt tartományt. Emellett hatással van az Azure AD-címtár és a felügyelt tartomány közötti szinkronizálásra is. A NSG létrehozása közben tartsa nyitva ezeket a portokat a szolgáltatás megszakadásának elkerülése érdekében.

### <a name="checking-your-nsg-for-compliance"></a>A NSG megfelelőségének ellenőrzése

1. Navigáljon a [hálózati biztonsági csoportok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) lapra a Azure Portal
2. A táblából válassza ki azt az alhálózathoz társított NSG, amelyben a felügyelt tartomány engedélyezve van.
3. A bal oldali panel **Beállítások** területén kattintson a **bejövő biztonsági szabályok** elemre.
4. Tekintse át a szabályokat, és határozza meg, hogy mely szabályok blokkolják a hozzáférést [ezekhez a portokhoz](network-considerations.md#network-security-groups-and-required-ports)
5. Szerkessze a NSG, és győződjön meg arról, hogy a szabály törlése, szabály hozzáadása vagy új NSG létrehozása teljes mértékben megtörténjen. A [szabály hozzáadásának](#add-a-rule-to-a-network-security-group-using-the-azure-portal) vagy új, megfelelő NSG létrehozásának lépései alább találhatók

## <a name="sample-nsg"></a>Minta NSG
Az alábbi táblázat egy olyan NSG ábrázol, amely a felügyelt tartomány védelmét fogja biztosítani, miközben lehetővé teszi a Microsoft számára az információk figyelését, kezelését és frissítését.

![minta NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services a virtuális hálózat nem korlátozott kimenő hozzáférését igényli. Azt javasoljuk, hogy ne hozzon létre olyan további NSG-szabályt, amely korlátozza a virtuális hálózat kimenő hozzáférését.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Szabály hozzáadása hálózati biztonsági csoporthoz a Azure Portal használatával
Ha nem kívánja használni a PowerShellt, manuálisan is hozzáadhat egyetlen szabályt a NSG a Azure Portal használatával. A hálózati biztonsági csoportban lévő szabályok létrehozásához hajtsa végre a következő lépéseket:

1. Navigáljon a [hálózati biztonsági csoportok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) lapra a Azure Portal.
2. A táblából válassza ki azt az alhálózathoz társított NSG, amelyben a felügyelt tartomány engedélyezve van.
3. A bal oldali panel **Beállítások** területén kattintson a **bejövő biztonsági szabályok** vagy a **kimenő biztonsági szabályok**lehetőségre.
4. A szabály létrehozásához kattintson a **Hozzáadás** gombra, és töltse ki az adatokat. Kattintson az **OK** gombra.
5. Ellenőrizze, hogy létrejött-e a szabály a szabályok táblában.


## <a name="need-help"></a>Segítség
A [visszajelzések megosztásához és a támogatáshoz](contact-us.md)forduljon a Azure Active Directory Domain Services termék csapatához.
