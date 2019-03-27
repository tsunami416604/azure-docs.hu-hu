---
title: Ismerje meg a legújabb Azure Vendégoperációsrendszer-kiadásainak listáját |} A Microsoft Docs
description: A legújabb kiadás híreket és SDK-kompatibilitási az Azure Cloud Services vendég operációs rendszer.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/26/2019
ms.author: raiye
ms.openlocfilehash: 955ba0aae41d7e555c1248cf04073b1b6e3f2d4f
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499964"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Az Azure vendég operációs rendszerek kiadásai és SDK-kompatibilitási mátrixot
Itt, a legújabb Azure vendég operációs rendszer naprakész információkat és a Cloud Services-kiadások. Ez az információ segít a frissítési útvonal megtervezéséhez, mielőtt egy vendég operációs rendszer le van tiltva. Ha a szerepkörök használatára konfigurálja *automatikus* vendég operációs rendszer frissítése leírtak szerint [Azure vendég operációs rendszer frissítési beállítások][Azure Guest OS Update Settings], már nem létfontosságú, hogy elolvasta-e ezen a lapon.

> [!IMPORTANT]
> Ezen a lapon a Cloud Services webes és feldolgozói szerepkörök, amelyek egy vendég operációs rendszer épülve vonatkozik. Ugyanúgy **vonatkozik** iaas-beli virtuális gépeken.
>
>


> [!TIP]
>  Fizessen elő a [Vendég operációsrendszer-frissítés RSS-hírcsatorna] vendég operációs rendszer összes módosításáról a kellő időben értesítést szeretne kapni.
>
>

> [!IMPORTANT]
> Csak a 2 legújabb a vendég operációs rendszer támogatott és az Azure Portalon elérhető lesz.
>
>

A vendég operációs rendszer frissítése tesztadatkészletén? Ellenőrizze [ez] [ cloud updates] ki.

## <a name="news-updates"></a>Hírek

###### <a name="march-26-2019"></a>**2019. március 26.**
A március vendég operációs rendszer jelent meg.

###### <a name="march-12-2019"></a>**2019. március 12.**
A február vendég operációs rendszer jelent meg.

###### <a name="february-5-2019"></a>**2019. február 5.**
A január vendég operációs rendszer jelent meg.

###### <a name="january-24-2019"></a>**2019. január 24.**
Család 6 vendég operációs rendszer (Windows Server 2019) jelent meg.

###### <a name="january-7-2019"></a>**2019. január 7.**
A December vendég operációs rendszer jelent meg.

###### <a name="december-14-2018"></a>**2018. december 14.**
A November vendég operációs rendszer jelent meg.

###### <a name="november-8-2018"></a>**2018. november 8.**
Az október vendég operációs rendszer jelent meg.

###### <a name="october-12-2018"></a>**2018. október 12.**
A szeptember vendég operációs rendszer jelent meg.

## <a name="releases"></a>Kiadások

## <a name="family-6-releases"></a>Család 6-kiadások
**Windows Server 2019**

.NET-keretrendszer: 3.5, 4.7.2

> [!NOTE]
> A Windows Azure SDK for .NET - 3.0 letölthető [Itt][Windows Azure SDK].
>
>Telepítési lépéseket:
>1. Távolítsa el a MicrosoftAzureAuthoringTools*.msi minden régebbi verzióját
>2. Telepítse a [Azure SDK for .NET - 3.0][Windows Azure SDK]
>3. A gép újraindítása
>4. Új Felhőszolgáltatás-projekt létrehozása és a egy feldolgozói szerepkör hozzáadása
>5. Módosítsa az operációsrendszer-család 6 és a csomag létrehozása
>6. A csomag telepítése az Azure-bA az Azure portal vagy a Visual Studio használatával
>


| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum |
| --- | --- | --- |
| WA-GUEST-OS-6.5_201903-01 |2019. március 26. |POST 6.7 |
| WA-GUEST-OS-6.4_201902-01 |2019. március 12. |POST 6.6. |
|~~WA-GUEST-OS-6.3_201901-01~~ |2019. február 5. |2019. március 26. |
|~~WA-GUEST-OS-6.2_201812-01~~ |2019. január 24. |2019. március 12. |
|~~WA-GUEST-OS-6.1_201811-01~~ |2019. január 24. |2019. február 5. |

## <a name="family-5-releases"></a>5. családba tartozó kiadások
**Windows Server 2016**

.NET-keretrendszer: 3.5, 4.6.2

> [!NOTE]
> Az operációsrendszer-család 5 RDP-jelszónak legalább 10 karakterből kell lennie.
>


| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum |
| --- | --- | --- |
| WA-GUEST-OS-5.29_201903-01 |2019. március 26. |POST 5.31 |
| WA-GUEST-OS-5.28_201902-01 |2019. március 12. |POST 5.30 |
|~~WA-GUEST-OS-5.27_201901-01~~ |2019. február 5. |2019. március 26. |
|~~WA-GUEST-OS-5.26_201812-01~~ |2019. január 7. |2019. március 12. |
|~~WA-GUEST-OS-5.25_201811-01~~ |2018. december 14. |2019. február 5. |
|~~WA-GUEST-OS-5.24_201810-01~~ |2018. november 8. |2019. január 7. |
|~~WA-GUEST-OS-5.23_201809-01~~ |2018. október 12. |2018. december 14. |

## <a name="family-4-releases"></a>Család 4-kiadások
**Windows Server 2012 R2**

.NET-keretrendszer: 3.5, 4.5.1

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum |
| --- | --- | --- |
| WA-GUEST-OS-4.64_201903-01 |2019. március 26. |POST 4,66 |
| WA-GUEST-OS-4.63_201902-01 |2019. március 12. |POST 4.65 |
|~~WA-GUEST-OS-4.62_201901-01~~ |2019. február 5. |2019. március 26. |
|~~WA-GUEST-OS-4.61_201812-01~~ |2019. január 7. |2019. március 12. |
|~~WA-GUEST-OS-4.60_201811-01~~ |2018. december 14. |2019. február 5. |
|~~WA-GUEST-OS-4.59_201810-01~~ |2018. november 8. |2019. január 7. |
|~~WA-GUEST-OS-4.58_201809-01~~ |2018. október 12. |2018. december 14. |

## <a name="family-3-releases"></a>3-as család kiadások
**Windows Server 2012**

.NET-keretrendszer: 3.5, 4.5

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum |
| --- | --- | --- |
| WA-GUEST-OS-3.71_201903-01 |2019. március 26. |POST 3.73 |
| WA-GUEST-OS-3.70_201902-01 |2019. március 12. |POST 3.72 |
|~~WA-GUEST-OS-3.69_201901-01~~ |2019. február 5. |2019. március 26. |
|~~WA-GUEST-OS-3.68_201812-01~~ |2019. január 7. |2019. március 12. |
|~~WA-GUEST-OS-3.67_201811-01~~ |2018. december 14. |2019. február 5. |
|~~WA-GUEST-OS-3.66_201810-01~~ |2018. november 8. |2019. január 7. |
|~~WA-GUEST-OS-3.65_201809-01~~ |2018. október 12. |2018. december 14. |

## <a name="family-2-releases"></a>2 család kiadások
**Windows Server 2008 R2 SP1**

.NET-keretrendszer: 3.5 (tartalmazza a 2.0 és 3.0 verziót)

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum |
| --- | --- | --- |
| WA-GUEST-OS-2.84_201903-01 |2019. március 26. |POST 2.86 |
| WA-GUEST-OS-2.83_201902-01 |2019. március 12. |POST 2.85 |
|~~WA-GUEST-OS-2.82_201901-01~~ |2019. február 5. |2019. március 26. |
|~~WA-GUEST-OS-2.81_201812-01~~ |2019. január 7. |2019. március 12. |
|~~WA-GUEST-OS-2.80_201811-01~~ |2018. december 14. |2019. február 5. |
|~~WA-GUEST-OS-2.79_201810-01~~ |2018. november 8. |2019. január 7. |
|~~WA-GUEST-OS-2.78_201809-01~~ |2018. október 12. |2018. december 14. |

## <a name="msrc-patch-updates"></a>MSRC-javítás frissítések
A lista minden egyes havi vendég operációs rendszer kiadása a javítások [Itt][patches].

## <a name="sdk-support"></a>SDK-támogatás
Annak ellenére, hogy a [kivezetési szabályzata, az Azure SDK] [ retire policy sdk] azt jelzi, hogy csak a fenti 2.2-es verzió támogatott, a megadott Vendég operációsrendszer-családok lehetővé teszik, hogy korábbi verzióit. Mindig a legújabb támogatott SDK-t használja.

| Vendég operációsrendszer-család | Kompatibilis SDK-verziókra |
| --- | --- |
| 6 |Verzió 2.9.6+ |
| 5 |Verzió 2.9.5.1+ |
| 4 |2.1-es vagy újabb |
| 3 |1.8-as vagy újabb |
| 2 |1.3-as vagy újabb |
| 1 |1.0-s vagy újabb |

## <a name="guest-os-release-information"></a>A vendég operációs rendszer kiadással kapcsolatos információk
Vannak a kiadások a vendég operációs rendszer számára fontos három dátumok: **kiadási** dátum, **le van tiltva** dátuma, és **lejárati** dátum. A vendég operációs rendszer elérhető számít, ha a portálon és a vendég operációs rendszer célként választható ki. Ha a vendég operációs rendszer elérte a **le van tiltva** dátuma, a rendszer eltávolítja az Azure. Minden olyan felhőalapú szolgáltatásnak, hogy a vendég operációs rendszer azonban továbbra is működik szokásos módon.

Az ablak között a **le van tiltva** dátum és a **lejárati** dátum biztosít a, a puffer egyszerűen átmenet a egy vendég operációs rendszer egy újabb. Ha használ *automatikus* , a vendég operációs rendszer, mindig is a legújabb verziót, és nem kell aggódnia, hamarosan lejár.

Ha a **lejárati** dátum továbbítja, minden olyan Felhőszolgáltatáshoz, továbbra is használja, hogy a vendég operációs rendszer leállította, törlődnek, vagy kényszerített frissítése. További információ a kivezetési szabályzata [Itt][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>A vendég operációs rendszer termékcsalád-verzió magyarázata
A Vendég operációsrendszer-családok Microsoft Windows Server kiadott verzióján alapulnak. A vendég operációs rendszer az alapul szolgáló operációs rendszer, amely az Azure Cloud Services futtat. Minden egyes vendég operációs rendszer rendelkezik olyan család, verzióját és kiadási száma.

* **Vendég operációsrendszer-család**  
  A Windows Server operációs rendszer kiadás, amely egy vendég operációs rendszeren alapul. Ha például *3-as család* Windows Server 2012 rendszeren alapul.
* **A vendég operációs rendszer verziója**  
  Adott családba tartozó Vendég operációsrendszer-lemezképhez, valamint a megfelelő [Microsoft Security Response Center (MSRC)] [ msrc] javítást, a dátumot, a vendég operációs rendszer új verzió jön létre. Lehet, hogy nem minden javításokat tartalmaz.

    Számok számozása 0 és 1 növekmény minden alkalommal, amikor bekerül a frissítések új készletét. Záró nullák csak láthatók. Ha a fontos. Hogy a verzió 2.10 egy másik, sokkal újabb verziójú, mint a 2.1-es verzió.
* **Vendég operációsrendszer-kiadás**  
  Újbóli kiadása egy vendég operációs rendszer verziója. Újbóli akkor fordul elő, ha a Microsoft problémák talál; tesztelése során a változtatásokat. A legfrissebb kiadás mindig hatályon kívül helyez minden korábbi kiadásai, nyilvános vagy sem. Az Azure Portalon csak akkor engedélyezi a felhasználók számára egy adott verziójának legújabb kiadása. Egy előző kiadást futtató központi telepítések általában nem tartoznak kényszerített frissített függően a bejelentett hiba súlyosságát.

Az alábbi példában a 2 a család, 12 az verziója pedig "rel2" kiadása.

**Vendég operációs rendszer kiadási** – 2.12 rel2

**Ebben a kiadásban a konfigurációs karakterláncból** -WA-VENDÉG-operációsrendszer-2.12_201208-02

A vendég operációs rendszer konfigurációs karakterláncában együtt egy bemutató, mely MSRC javítások lettek figyelembe véve, kiadás dátuma, a beágyazott ugyanezeket az adatokat. Ebben a példában akár előállítása a Windows Server 2008 R2 és a köztük a 2012. augusztus MSRC javítások is venni. Csak a kifejezetten a Windows Server adott verziójával alkalmazása javításait is. Például egy MSRC javítás vonatkozik a Microsoft Office, ha azt nem lehet foglalt mert a termék már nem része a Windows Server alaplemezkép.

## <a name="guest-os-system-update-process"></a>Vendég operációs rendszer frissítési folyamata
Ezen a lapon a közelgő Vendégoperációsrendszer-kiadásainak listáját információkat biztosít. Ügyfelek azt jelezte, hogy szeretné-e tudja, mert azok a felhőszolgáltatásokhoz tartozó szerepkörök újraindul, ha a beállítás a "Automatikus" frissítési kiadási bekövetkezésekor. Kiadások a vendég operációs rendszer általában 2-3 hét a MSRC kiadás, amely minden hónap második keddjén frissítése után fordulhat elő. Új verziók tartalmazzák a megfelelő MSRC javításokat az egyes vendég operációsrendszer-család.

A Microsoft Azure folyamatosan frissítéseket ad ki. A vendég operációs rendszer csak egy ilyen frissítés folyamatban. A kiadás számos tényezőtől hatással lehet az itt felsorolt túl sok. Az Azure emellett több ezer gépet szó szerint több száz futtat. Ez azt jelenti, hogy nem lehetséges a pontos dátumot és időt, amikor a szerepkör újraindul. Dolgozunk a csomag korlátozható vagy újraindítások idő.

Ha a vendég operációs rendszer új kiadása közzé van téve, teljes mértékben Azure-ban propagálása időbe telhet. Szolgáltatás frissítve lett, hogy az új vendég operációs rendszer, mivel azok újraindulásakor érvényesítenie frissítési tartománnyal. Szolgáltatások "Automatikus" frissítések használatához állítsa a kiadási első fog kapni. A frissítés után látni fogja az új vendég operációs rendszer verzió a szolgáltatás az Azure Portalon ezeket tájékoztatási céllal felsorolja. Ebben az időszakban újból kiadott biztonsági frissítések fordulhat elő. Egyes verziói hosszabb idő alatt is üzembe helyezhetők, és az automatikus frissítési újraindítások nem fordulhat elő, a hivatalos kiadás dátumot követően számos hétig. A vendég operációs rendszer nem érhető el, ha explicit módon kiválaszthatja azt a verziót a portálon vagy a konfigurációs fájlban.

Ezután újraindul, és további információkat technikai részletek Vendég és gazdagép operációs rendszerének frissítések mutatók értékes információk nagy fokú, lásd az MSDN blogbejegyzésében [szerepkör példány újraindítása miatt operációs rendszer Verziófrissítései] [ restarts].

Ha manuálisan frissíti a vendég operációs rendszer, tekintse meg a [vendég operációs rendszer kivezetési szabályzata] [ retirepolicy] további információt.

## <a name="guest-os-supportability-and-retirement-policy"></a>A vendég operációs rendszer támogatási és kivezetési szabályzat
A vendég operációs rendszer támogatási és kivezetési szabályzat kifejtett [Itt][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Vendég operációsrendszer-frissítés RSS-hírcsatorna]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
