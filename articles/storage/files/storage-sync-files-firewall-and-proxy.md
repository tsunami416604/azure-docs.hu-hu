---
title: Azure fájl szinkronizálása a helyszíni tűzfal és proxy beállításainak |} Microsoft Docs
description: Azure fájl szinkronizálása a helyszíni hálózati konfigurációja
services: storage
documentationcenter: ''
author: fauhse
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 5014c8204b6b6da539a41aaa3308d8787fb517a7
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738530"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Az Azure File Sync proxy- és tűzfalbeállításai
Azure fájl szinkronizálása a helyszíni kiszolgálók csatlakozik Azure fájlok többhelyes szinkronizálás és a felhőalapú szolgáltatások rétegezéséhez engedélyezése. Egy helyszíni kiszolgálón, kapcsolódnia kell az internethez. Rendszergazda döntse el, hogy a legjobb útvonalat az Azure felhőszolgáltatások elérni kívánt kiszolgáló van szüksége.

Ez a cikk különleges követelményeket előíró lehetőség legyen elérhető a sikeresen és biztonságosan csatlakoztassa a kiszolgálót Azure fájlszinkronizálás betekintést nyújt.

> [!Important]
> Azure fájl szinkronizálása még támogatja tűzfalak és a virtuális hálózatok a tárfiókhoz. 

## <a name="overview"></a>Áttekintés
Azure fájlszinkronizálás úgy működik, mint az orchestration szolgáltatás a Windows Server, az Azure fájlmegosztások és több más Azure-szolgáltatásokkal szinkronizálni az adatokat, lásd: a szinkronizálási csoport között. Az Azure-fájl szinkronizálás megfelelően működjön szüksége lesz a következő Azure szolgáltatásokkal kommunikálni a kiszolgálók konfigurálása:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Hitelesítési szolgáltatások

> [!Note]  
> Az Azure fájlszinkronizálás ügynök a Windows Server indít el a felhőalapú szolgáltatások, így csak a kimenő forgalom tűzfal szempontból fontolja meg kellene minden kérelemhez. <br /> Nincs Azure-szolgáltatások az Azure fájlszinkronizálás ügynök kapcsolatot kezdeményez.


## <a name="ports"></a>Portok
Azure fájlszinkronizálás helyezi át a fájladatok és metaadatok kizárólag HTTPS-en keresztül, és 443-as portot kell megnyitni kimenő igényel.
Ennek eredményeképpen az összes forgalom titkosítva van.

## <a name="networks-and-special-connections-to-azure"></a>Hálózatok és az Azure-bA speciális kapcsolatok
Az Azure fájlszinkronizálás ügynök követelményei nem vonatkozó speciális csatornákat, például [ExpressRoute](../../expressroute/expressroute-introduction.md), az Azure-bA stb.

Az Azure fájlszinkronizálás keresztül a rendelkezésre álló eszközöket, amelyek lehetővé teszik az Azure automatikusan különböző hálózati jellemzőit, például a sávszélességgel, a késés történő, valamint a pontosabb beállításra felügyeleti vezérlő ajánlat reach fog működni. Nem minden funkció jelenleg érhető el. Ha szeretné konfigurálni az adott viselkedést, ossza meg velünk keresztül [Azure fájlok UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure fájlszinkronizálás jelenleg érvényes proxybeállításokat. A proxybeállítást nem átlátszó Azure fájlszinkronizálás ügynökkel, mert a teljes kiszolgáló továbbítódik a proxyn keresztül.

Alkalmazás-specifikus proxybeállítások jelenleg fejlesztés alatt, és egy későbbi kiadásban az Azure fájlszinkronizálás ügynök támogatott. Ez lehetővé teszi a kifejezetten az Azure fájlszinkronizálás forgalom esetén a proxy konfigurációját.

## <a name="firewall"></a>Tűzfal
Ahogyan az előző szakaszban, kimenő megnyitása port 443 kell lennie. A datacenter, a fiókiroda vagy a régió házirendek alapján, további korlátozása adott tartományokra porton keresztüli forgalmat esetleg szükséges, vagy szükséges.

A következő táblázat ismerteti a szükséges kommunikációs tartományokban:

| Szolgáltatás | Tartomány | Használat |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Bármely felhasználó hívás (például a PowerShell) végighalad a/az URL-cím, beleértve a kezdeti kiszolgáló regisztrációs hívás. |
| **Azure Active Directory** | https://login.windows.net | Az Azure Resource Manager hívások hitelesített felhasználónak kell kezdeményeznie. Sikeres, a felhasználók hitelesítéséhez az URL-cím szolgál ki. |
| **Azure Active Directory** | https://graph.windows.net/ | Azure fájlszinkronizálás történő telepítésének részeként az előfizetés Azure Active Directoryban egy egyszerű jön létre. Az URL-cím, amely használható. Az egyszerű delegálása jogosultsága ahhoz, hogy a fájl szinkronizálási Azure szolgáltatás minimális számú szolgál. A felhasználó Azure fájlszinkronizálás a kezdeti telepítés végrehajtása egy hitelesített előfizetés tulajdonosának jogosultságokkal rendelkező felhasználónak kell lennie. |
| **Azure Storage** | &ast;.core.windows.net | Amikor a rendszer letölti a fájlt, majd a kiszolgáló hajtja adott adatmozgás hatékonyabban közvetlenül az Azure-tárfiókban fájlmegosztáshoz való. A kiszolgáló rendelkezik egy SAS-kulcsot, amely csak a megcélzott fájl megosztás eléréséhez. |
| **Azure File Sync** | &ast;.one.microsoft.com | Kezdeti kiszolgáló regisztrálása után a kiszolgáló egy Azure fájlszinkronizálás szolgáltatáspéldány regionális URL-címet kap az adott régióban. A kiszolgáló az URL-cím használatával közvetlenül és hatékonyan tudja kommunikálni a példány, a szinkronizálási kezelése. |

> [!Important]
> Amikor átengedi a forgalmat a &ast;. one.microsoft.com, nem csak a szinkronizálási szolgáltatás felé irányuló forgalom lehetőség a kiszolgálóról. Nincsenek elérhető altartományok alatt számos további Microsoft-szolgáltatás.

Ha &ast;. one.microsoft.com túl széleskörű, csak explicit regionális az Azure fájlok szinkronizáló szolgáltatás példányainak tételével korlátozhatja a kiszolgálói kommunikációhoz. Mely-példányokat, kiválaszthatja a tároló szinkronizálási szolgáltatás, amelyhez telepítését és a kiszolgáló regisztrálva régiója függ. Ez a régió, engedélyeznie kell a kiszolgálóhoz. Hamarosan nem lesznek további URL-címek engedélyezése az új üzleti folytonosságot biztosító szolgáltatásokat. 

| Régió | Az Azure fájlszinkronizálás regionális végponti URL-cím |
|--------|---------------------------------------|
| Kelet-Ausztrália | https://kailani-aue.one.microsoft.com |
| Közép-Kanada | https://kailani-cac.one.microsoft.com |
| USA keleti régiója | https://kailani1.one.microsoft.com |
| Délkelet-Ázsia | https://kailani10.one.microsoft.com |
| Az Egyesült Királyság déli régiója | https://kailani-uks.one.microsoft.com |
| Nyugat-Európa | https://kailani6.one.microsoft.com |
| USA nyugati régiója | https://kailani.one.microsoft.com |

> [!Important]
> Részletes tűzfalszabály határozza meg, ha ez a dokumentum gyakran ellenőrizze, és a tűzfal beállításait a listaelemek elavult vagy hiányos URL-cím miatt folyamatossága érdekében a tűzfalszabályainak frissítése.

## <a name="summary-and-risk-limitation"></a>Összegzés és kockázatkezelési korlátozása
A listák a jelen dokumentum korábbi Azure fájl szinkronizálási szolgáltatás jelenleg kommunikál az URL-címeket tartalmaz. Tűzfalak ezeket a tartományokat, valamint a válaszok tőlük kimenő forgalmát engedélyező képesnek kell lennie. Microsoft nagy hangsúlyt fektet tartsa a lista frissítése.

Tartomány korlátozása tűzfalszabályok beállítása lehet egy mérték biztonság növelése érdekében. A tűzfal-konfigurációk használata esetén egy kell vegye figyelembe, hogy URL-címek fel és idővel megváltozott. Ezért legyen körültekintő mértéket ellenőrizze a jelen dokumentum a táblák egy változáskezelési folyamatot egy Azure fájlszinkronizálás ügynök verzióról a másikra a legújabb ügynököt próbatelepítés részeként. Így biztosítható, hogy a tűzfal beállításai tartományokba irányuló forgalom a legújabb ügynököt engedélyezése szükséges.

## <a name="next-steps"></a>További lépések
- [Egy Azure fájlszinkronizálás központi telepítésének tervezése](storage-sync-files-planning.md)
- [Azure fájlszinkronizálás (előzetes verzió) telepítése](storage-sync-files-deployment-guide.md)
