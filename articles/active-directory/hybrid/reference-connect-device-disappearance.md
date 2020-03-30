---
title: Az Azure AD Connect 1.4.xx.x és az eszközök eltűnésének ismertetése | Microsoft dokumentumok
description: Ez a dokumentum az Azure AD Connect 1.4.xx.x verziójával kapcsolatos problémát ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176025"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Az Azure AD Connect 1.4.xx.x és az eszközök eltűnésének ismertetése
Az Azure AD Connect 1.4.xx.x verziójával egyes ügyfelek láthatják, hogy windowsos eszközeik egy része vagy egésze eltűnik az Azure AD-ből. Ez nem ad okot aggodalomra, mivel ezeket az eszközidentitásokat az Azure AD nem használja a feltételes hozzáférés engedélyezése során. Ez a módosítás nem törli azokat a Windows-eszközöket, amelyek megfelelően regisztráltak az Azure AD hibrid Azure AD-s csatlakozáshoz.

Ha az Azure AD-ben az eszközobjektumok törlése meghaladja az exportálási törlési küszöbértéket, azt javasoljuk, hogy az ügyfél engedélyezze a törléseket. [Útmutató: engedélyezze a törlési folyamatot, ha túllépik a törlési küszöbértéket](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Háttér
A hibrid Azure AD-hez csatlakozott rendszerként regisztrált Windows-eszközök eszközobjektumként jelennek meg az Azure AD-ben. Ezek az eszközobjektumok feltételes hozzáféréshez használhatók. A Windows 10-es eszközök az Azure AD Connecten keresztül szinkronizálódnak a felhőbe, a lefelé irányuló szintű Windows-eszközök közvetlenül az AD FS vagy a zökkenőmentes egyszeri bejelentkezés használatával regisztrálhatók.

## <a name="windows-10-devices"></a>Windows 10-eszközök
Csak a Hibrid Azure AD Join által konfigurált adott userCertificate attribútumértékkel rendelkező Windows 10-es eszközöket kell szinkronizálnia a felhőbe az Azure AD Connect. Az Azure AD Connect korábbi verzióiban ezt a követelményt nem érvényesítették szigorúan, ami szükségtelen eszközobjektumokat eredményezaz Azure AD-ben. Az ilyen eszközök az Azure AD-ben mindig a "függőben" állapotban maradt, mert ezek az eszközök nem voltak célja, hogy regisztrálva legyenek az Azure AD-vel. 

Az Azure AD Connect ezen verziója csak azokkal a Windows 10-es eszközökkel szinkronizálja a rendszert, amelyek megfelelően vannak konfigurálva hibrid Azure AD-csatlakozásra. Windows 10 device objects without the Azure AD join specific userCertificate will be removed from Azure AD.

## <a name="down-level-windows-devices"></a>Lecsökkentő Windows-eszközök
Az Azure AD Connect soha nem szinkronizálhat [lefelé érő Windows-eszközöket.](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices) Az Azure AD-ben korábban helytelenül szinkronizált eszközök most törlődnek az Azure AD-ből. Ha az Azure AD Connect lefelé [irányuló Windows-eszközök](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)törlését próbálja meg, akkor az eszköz nem az, amelyet a Microsoft Workplace Join nem [Windows 10-es számítógépekMSI számára](https://www.microsoft.com/download/details.aspx?id=53554) hozott létre, és nem használható fel más Azure AD-szolgáltatás.

Egyes ügyfelek előfordulhat, hogy újra [hogyan: Tervezze meg a hibrid Azure Active Directory-csatlakozás megvalósítása](../devices/hybrid-azuread-join-plan.md) a Windows-eszközök megfelelő regisztrálása, és győződjön meg arról, hogy az ilyen eszközök teljes mértékben részt vehetnek az eszköz-alapú feltételes hozzáférés. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Hogyan ellenőrizhetem, hogy mely eszközök törlődnek ezzel a frissítéssel?

A törölt eszközök ellenőrzéséhez használhatja ezt a PowerShell-parancsfájlt:https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Ez a parancsfájl jelentést készít az Active Directory számítógép-objektumokban tárolt tanúsítványokról, különösen a hibrid Azure AD-illesztési szolgáltatás által kiadott tanúsítványokról.
Ellenőrzi az AD-ben lévő számítógép-objektum UserCertificate tulajdonságában található tanúsítványokat, és minden egyes lejárt tanúsítvány esetében ellenőrzi, hogy a tanúsítványt a hibrid Azure AD-illesztési szolgáltatáshoz állították-e ki (azaz a tulajdonos neve megegyezik a CN={ObjectGUID} minősítéssel).
Mielőtt az Azure AD Connect szinkronizálna az Azure AD bármely olyan számítógéppel, amely legalább egy érvényes tanúsítványt tartalmazott, de az Azure AD Connect 1.4-es verziójától kezdve, a szinkronizálási motor képes azonosítani a hibrid Azure AD illesztési tanúsítványokat, és "cloudfilter" a számítógép-objektum szinkronizálása az Azure AD, kivéve, ha van egy érvényes hibrid Azure AD-csatlakozási tanúsítvány.
Az AD-vel már szinkronizált, de érvényes hibrid Azure AD-csatlakozási tanúsítvánnyal nem rendelkező Azure AD-eszközök törlődnek (CloudFiltered=TRUE) a szinkronizálási motor által.

## <a name="next-steps"></a>Következő lépések
- [Az Azure AD Connect verzióelőzményei](reference-connect-version-history.md)
