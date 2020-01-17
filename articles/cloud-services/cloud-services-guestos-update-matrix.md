---
title: Ismerje meg az Azure vendég operációs rendszerének legújabb kiadásait | Microsoft Docs
description: Az Azure Cloud Services vendég operációs rendszerhez készült legújabb hírek és SDK-kompatibilitás.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 1/15/2020
ms.author: raiye
ms.openlocfilehash: 1139e2b26cdc1dee524b1d3b6a942c735c43aec7
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122245"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure vendég operációs rendszerek kiadásai és SDK-kompatibilitási mátrix
Naprakész információkat biztosít a Cloud Services legújabb Azure Guest OS-kiadásairól. Ez az információ segít megtervezni a frissítési útvonalat, mielőtt a vendég operációs rendszer le lenne tiltva. Ha úgy konfigurálja a szerepköröket, hogy az *automatikus* vendég operációs rendszer frissítéseit használják az [Azure vendég operációs rendszer frissítési beállításai][Azure Guest OS Update Settings]részében leírtak szerint, nem létfontosságú, hogy elolvassa ezt a lapot.

> [!IMPORTANT]
> Ez a lap Cloud Services webes és feldolgozói szerepkörökre vonatkozik, amelyek egy vendég operációs rendszeren futnak. A IaaS Virtual Machinesra **nem vonatkozik** .
>
>


> [!TIP]
>  Fizessen elő a [Vendég operációs rendszer frissítése RSS-hírcsatorna] , hogy megkapja a legtöbb időben érkező értesítést a vendég operációs rendszer változásairól.
>
>

> [!IMPORTANT]
> A Azure Portalben csak a vendég operációs rendszer legújabb 2 verziója lesz támogatott és elérhető.
>
>

Nem biztos benne, hogy miként lehet frissíteni a vendég operációs rendszert? Tekintse meg [ezt a][cloud updates] kivételt.

## <a name="news-updates"></a>Hírek frissítései

###### <a name="january-8-2020"></a>**2020. január 8.**
A decemberi vendég operációs rendszer megjelent.

###### <a name="december-5-2019"></a>**2019. december 5.**
A november vendég operációs rendszer megjelent.

###### <a name="november-1-2019"></a>**November 1., 2019**
A vendég operációs rendszer októberi kiadása megtörtént.

###### <a name="october-7-2019"></a>**Október 7., 2019**
A szeptemberi vendég operációs rendszer megjelent.

###### <a name="september-4-2019"></a>**Szeptember 4., 2019**
Az augusztusi vendég operációs rendszer megjelent.

###### <a name="july-26-2019"></a>**2019. július 26.**
A júliusi vendég operációs rendszer megjelent.

###### <a name="july-8-2019"></a>**2019. július 8.**
A júniusi vendég operációs rendszer megjelent.

###### <a name="june-6-2019"></a>**2019. június 6.**
A lehetséges vendég operációs rendszer megjelent.

###### <a name="may-7-2019"></a>**2019. május 7.**
A vendég operációs rendszer április óta megjelent.

###### <a name="march-26-2019"></a>**Március 26., 2019**
A márciusi vendég operációs rendszer megjelent.

###### <a name="march-12-2019"></a>**2019. március 12.**
A februári vendég operációs rendszer megjelent.

###### <a name="february-5-2019"></a>**Február 5., 2019**
A januári vendég operációs rendszer megjelent.

###### <a name="january-24-2019"></a>**2019. január 24.**
A Family 6 vendég operációs rendszer (Windows Server 2019) már megjelent.

###### <a name="january-7-2019"></a>**2019. január 7.**
A decemberi vendég operációs rendszer megjelent.

###### <a name="december-14-2018"></a>**December 14., 2018**
A november vendég operációs rendszer megjelent.

###### <a name="november-8-2018"></a>**November 8., 2018**
A vendég operációs rendszer októberi kiadása megtörtént.

###### <a name="october-12-2018"></a>**2018. október 12.**
A szeptemberi vendég operációs rendszer megjelent.

## <a name="releases"></a>Kiadások

## <a name="family-6-releases"></a>6\. család kiadásai
**Windows Server 2019**

.NET-keretrendszer telepítve: 3,5, 4.7.2

> [!NOTE]
> [Itt][Windows Azure SDK]tölthető le a Windows Azure SDK for .net-3,0.
>
>Telepítési lépések:
>1. Távolítsa el a MicrosoftAzureAuthoringTools*. msi régebbi verzióit
>2. A [.net-hez készült Azure SDK telepítése – 3,0][Windows Azure SDK]
>3. A gép újraindítása
>4. Hozzon létre egy új Cloud Service-projektet, és vegyen fel egyetlen feldolgozói szerepkört.
>5. Az operációsrendszer-család módosítása 6-ra és csomag összeállítása
>6. A csomag üzembe helyezése az Azure-ban a Azure Portal vagy a Visual Studio használatával
>


| Konfigurációs sztring | Kiadási dátum | Dátum letiltása |
| --- | --- | --- |
| WA-GUEST-OS-6.14 _201912-01 | 2020. január 8. | 6,16 post |  
| WA-GUEST-OS-6.13 _201911-01 | 2019. december 5. | 6,15 post |  
|~~WA-GUEST-OS-6,12 _201910-01~~| 2019. november 1. | 2020. január 8. |  
|~~WA-GUEST-OS-6.11 _201909-01~~| Október 7., 2019 | 2019. december 5. |  
|~~WA-GUEST-OS-6,10 _201908-01~~| 2019. augusztus 4. | 2019. november 1.  |  
|~~WA-GUEST-OS-6,9 _201907 – 0~~|2019. július 26. | Október 7., 2019 |
|~~WA-GUEST-OS-6,8 _201906-01~~|2019. július 8. |2019. augusztus 4. |
|~~WA-GUEST-OS-6,7 _201905-01~~ |2019. június 6. |2019. július 26. |
|~~WA-GUEST-OS-6.6 _201904-01~~ |2019. május 7. |2019. július 8. |
|~~WA-GUEST-OS-6.5 _201903-01~~ |Március 26., 2019 |2019. június 6. |
|~~WA-GUEST-OS-6.4 _201902-01~~ |2019. március 12. |2019. május 7. |
|~~WA-GUEST-OS-6.3 _201901-01~~ |Február 5., 2019 |Március 26., 2019 |
|~~WA-GUEST-OS-6.2 _201812-01~~ |2019. január 24. |2019. március 12. |
|~~WA-GUEST-OS-6.1 _201811-01~~ |2019. január 24. |Február 5., 2019 |

## <a name="family-5-releases"></a>5\. család kiadásai
**Windows Server 2016**

.NET-keretrendszer telepítve: 3,5, 4,6

> [!NOTE]
> Az operációsrendszer-család 5-ös verziójának RDP-jelszavának legalább 10 karakterből kell állnia.
>


| Konfigurációs sztring | Kiadási dátum | Dátum letiltása |
| --- | --- | --- |
| WA-GUEST-OS-5.38 _201912-01 | 2020. január 8. | 5,40 post |  
| WA-GUEST-OS-5.37 _201911-01 | 2019. december 5. | 5,39 post |  
|~~WA-GUEST-OS-5.36 _201910-01~~| 2019. november 1. | 2020. január 8. |  
|~~WA-GUEST-OS-5.35 _201909-01~~| Október 7., 2019 | 2019. december 5. |  
|~~WA-GUEST-OS-5.34 _201908-01~~|  2019. augusztus 4.  | 2019. november 1. |  
|~~WA-GUEST-OS-5.33 _201907-01~~| 2019. július 26. | Október 7., 2019 |  
|~~WA-GUEST-OS-5.32 _201906-01~~|2019. július 8. |2019. augusztus 4. |
|~~WA-GUEST-OS-5.31 _201905-01~~ |2019. június 6. |2019. július 26. |
|~~WA-GUEST-OS-5.30 _201904-01~~ |2019. május 7. |2019. július 8. |
|~~WA-GUEST-OS-5.29 _201903-01~~ |Március 26., 2019 |2019. június 6. |
|~~WA-GUEST-OS-5.28 _201902-01~~ |2019. március 12. |2019. május 7. |
|~~WA-GUEST-OS-5.27 _201901-01~~ |Február 5., 2019 |Március 26., 2019 |
|~~WA-GUEST-OS-5.26 _201812-01~~ |2019. január 7. |2019. március 12. |
|~~WA-GUEST-OS-5.25 _201811-01~~ |December 14., 2018 |Február 5., 2019 |
|~~WA-GUEST-OS-5.24 _201810-01~~ |2018. november 8. |2019. január 7. |
|~~WA-GUEST-OS-5.23 _201809-01~~ |2018. október 12. |December 14., 2018 |

## <a name="family-4-releases"></a>4\. család kiadásai
**Windows Server 2012 R2**

.NET-keretrendszer telepítve: 3,5, 4.5.1, 4.5.2

| Konfigurációs sztring | Kiadási dátum | Dátum letiltása |
| --- | --- | --- |
| WA-GUEST-OS-4.73 _201912-01 | 2020. január 8. | 4,75 post |  
| WA-GUEST-OS-4.72 _201911-01 | 2019. december 5. | 4,74 post |  
|~~WA-GUEST-OS-4.71 _201910-01~~| 2019. november 1. | 2020. január 8. |  
|~~WA-GUEST-OS-4.70 _201909-01~~| Október 7., 2019 | 2019. december 5. |  
|~~WA-GUEST-OS-4.69 _201908-01~~| 2019. augusztus 4. | 2019. november 1. |  
|~~WA-GUEST-OS-4.68 _201907-01~~| 2019. július 26.  | Október 7., 2019 |
|~~WA-GUEST-OS-4.67 _201906-01~~| 2019. július 8. |2019. augusztus 4. |
|~~WA-GUEST-OS-4.66 _201905-01~~ |2019. június 6. |2019. július 26. |
|~~WA-GUEST-OS-4.65 _201904-01~~ |2019. május 7. |2019. július 8. |
|~~WA-GUEST-OS-4.64 _201903-01~~ |Március 26., 2019 |2019. június 6. |
|~~WA-GUEST-OS-4.63 _201902-01~~ |2019. március 12. |2019. május 7. |
|~~WA-GUEST-OS-4.62 _201901-01~~ |Február 5., 2019 |Március 26., 2019 |
|~~WA-GUEST-OS-4.61 _201812-01~~ |2019. január 7. |2019. március 12. |
|~~WA-GUEST-OS-4.60 _201811-01~~ |December 14., 2018 |Február 5., 2019 |
|~~WA-GUEST-OS-4.59 _201810-01~~ |2018. november 8. |2019. január 7. |
|~~WA-GUEST-OS-4.58 _201809-01~~ |2018. október 12. |December 14., 2018 |

## <a name="family-3-releases"></a>3\. család – kiadások
**Windows Server 2012**

.NET-keretrendszer telepítve: 3,5, 4,5

| Konfigurációs sztring | Kiadási dátum | Dátum letiltása |
| --- | --- | --- |
| WA-GUEST-OS-3.80 _201912-01 | 2020. január 8. | 3,82 post |  
| WA-GUEST-OS-3.79 _201911-01 | 2019. december 5. | 3,81 post |  
|~~WA-GUEST-OS-3.78 _201910-01~~| 2019. november 1. | 2020. január 8. |  
|~~WA-GUEST-OS-3.77 _201909-01~~| Október 7., 2019 | 2019. december 5. |  
|~~WA-GUEST-OS-3.76 _201908-01~~|  2019. augusztus 4.  |  2019. november 1.  |  
|~~WA-GUEST-OS-3,75 _201907-01~~| 2019. július 26. | Október 7., 2019 |
|~~WA-GUEST-OS-3.74 _201906-01~~| 2019. július 8. |2019. augusztus 4. |
|~~WA-GUEST-OS-3.73 _201905-01~~ |2019. június 6. |2019. július 26. |
|~~WA-GUEST-OS-3.72 _201904-01~~ |2019. május 7. |2019. július 8. |
|~~WA-GUEST-OS-3.71 _201903-01~~ |Március 26., 2019 |2019. június 6. |
|~~WA-GUEST-OS-3.70 _201902-01~~ |2019. március 12. |2019. május 7. |
|~~WA-GUEST-OS-3.69 _201901-01~~ |Február 5., 2019 |Március 26., 2019 |
|~~WA-GUEST-OS-3.68 _201812-01~~ |2019. január 7. |2019. március 12. |
|~~WA-GUEST-OS-3.67 _201811-01~~ |December 14., 2018 |Február 5., 2019 |
|~~WA-GUEST-OS-3.66 _201810-01~~ |2018. november 8. |2019. január 7. |
|~~WA-GUEST-OS-3.65 _201809-01~~ |2018. október 12. |December 14., 2018 |

## <a name="family-2-releases"></a>2\. család kiadásai
**Windows Server 2008 R2 SP1**

.NET-keretrendszer telepítve: 3,5 (tartalmazza a 2,0 és 3,0), 4,5

| Konfigurációs sztring | Kiadási dátum | Dátum letiltása |
| --- | --- | --- |
| WA-GUEST-OS-2.93 _201912-01 | 2020. január 8. | 2,95 post |  
| WA-GUEST-OS-2.92 _201911-01 | 2019. december 5. | 2,94 post |  
|~~WA-GUEST-OS-2.91 _201910-01~~| 2019. november 1. | 2020. január 8. |  
|~~WA-GUEST-OS-2.90 _201909-01~~| Október 7., 2019 | 2019. december 5. |  
|~~WA-GUEST-OS-2.89 _201908-01~~| 2019. augusztus 4. | 2019. november 1. |  
|~~WA-GUEST-OS-2,88 _201907-01~~| 2019. július 26. | Október 7., 2019 |
|~~WA-GUEST-OS-2.87 _201906-01~~|2019. július 8. | 2019. augusztus 4. |
|~~WA-GUEST-OS-2.86 _201905-01~~ |2019. június 6. |2019. július 26. |
|~~WA-GUEST-OS-2.85 _201904-01~~ |2019. május 7. |2019. július 8. |
|~~WA-GUEST-OS-2.84 _201903-01~~ |Március 26., 2019 |2019. június 6. |
|~~WA-GUEST-OS-2.83 _201902-01~~ |2019. március 12. |2019. május 7. |
|~~WA-GUEST-OS-2.82 _201901-01~~ |Február 5., 2019 |Március 26., 2019 |
|~~WA-GUEST-OS-2.81 _201812-01~~ |2019. január 7. |2019. március 12. |
|~~WA-GUEST-OS-2.80 _201811-01~~ |December 14., 2018 |Február 5., 2019 |
|~~WA-GUEST-OS-2.79 _201810-01~~ |2018. november 8. |2019. január 7. |
|~~WA-GUEST-OS-2.78 _201809-01~~ |2018. október 12. |December 14., 2018 |

## <a name="msrc-patch-updates"></a>MSRC-javítások frissítései
A havi vendég operációs rendszer kiadásaiban található javítások listája [itt][patches]érhető el.

## <a name="sdk-support"></a>SDK-támogatás
Annak ellenére, hogy az [Azure SDK][retire policy sdk] -ra vonatkozó kivezetési szabályzat azt jelzi, hogy csak a 2,2-es verzió támogatott, a vendég operációsrendszer-családok a korábbi verziók használatát teszik lehetővé. Mindig a legújabb támogatott SDK-t használja.

| Vendég operációs rendszer családja | Kompatibilis SDK-verziók |
| --- | --- |
| 6 |Verzió 2.9.6 + |
| 5 |Verzió 2.9.5.1 + |
| 4 |2\.1-es verzió + |
| 3 |1\.8-as verzió + |
| 2 |1\.3-as és újabb verzió |
| 1 |1\.0-s verzió + |

## <a name="guest-os-release-information"></a>Vendég operációs rendszer kiadásával kapcsolatos információk
A vendég operációs rendszer kiadásai három fontos dátummal rendelkeznek: **kiadás** dátuma, **letiltott** dátum és **lejárati** dátum. A vendég operációs rendszer akkor tekinthető elérhetőnek, ha a portálon van, és a cél vendég operációs rendszerként is kiválasztható. Ha a vendég operációs rendszer eléri a **letiltott** dátumot, az el lesz távolítva az Azure-ból. Azonban a vendég operációs rendszer által megcélzott felhőalapú szolgáltatások továbbra is a megszokott módon működnek.

A **letiltott** dátum és a **lejárati** dátum közötti ablak egy puffert biztosít az egyik vendég operációs rendszerről egy újabbra való áttéréshez. Ha a vendég operációs rendszerként *automatikusan* használja a szolgáltatást, mindig a legújabb verziót fogja használni, és nem kell aggódnia.

A **lejárati dátum lejárta** után minden olyan felhőalapú szolgáltatást, amely továbbra is a vendég operációs rendszert használja, a rendszer leállítja, törli vagy kikényszeríti a frissítést. További információkat a kivezetési szabályzatról [itt][retirepolicy]talál.

## <a name="guest-os-family-version-explanation"></a>Vendég operációsrendszer-család verziója – magyarázat
A vendég operációsrendszer-családok a Microsoft Windows Server kiadott verzióin alapulnak. A vendég operációs rendszer az a mögöttes operációs rendszer, amelyen az Azure Cloud Services fut. Minden vendég operációs rendszer rendelkezik egy családtal, egy verzióval és egy kiadási számmal.

* **Vendég operációs rendszer családja**  
  Egy Windows Server operációs rendszer kiadása, amelyet a vendég operációs rendszer alapul. A 3. *család* például a Windows Server 2012-alapú.
* **Vendég operációs rendszer verziója**  
  A vendég operációsrendszer-család rendszerképére és a [Microsoft Security Response Center (MSRC)][msrc] azon javításait, amelyek az új vendég operációs rendszer előállításának napján érhetők el. A javítások nem mindegyike szerepelhet.

    A számok a 0 értéknél kezdődnek, és minden alkalommal eggyel nagyobbak, amikor új frissítéseket adnak hozzá. A záró nullák csak akkor jelennek meg, ha fontosak. Az 2,10-es verzió a 2,1-es verziótól eltérő, jóval újabb verzió.
* **Vendég operációs rendszer kiadása**  
  A vendég operációs rendszer verziójának újbóli kiadása. Ha A Microsoft hibát észlel A tesztelés során, A rendszer újrakiadást hoz létre. változtatások megkövetelése. A legújabb kiadás mindig felülír minden korábbi kiadást, nyilvános vagy nem. A Azure Portal csak egy adott verzió legújabb kiadását fogja engedélyezni a felhasználóknak. Az előző kiadáson futó központi telepítések általában nem kényszerítve vannak a hiba súlyossági szintjétől függően.

Az alábbi példában a 2 a család, a 12 a verzió, a "rel2" pedig a kiadás.

**Vendég operációs rendszer kiadása** – 2,12 rel2

A **kiadás konfigurációs karakterlánca** -WA-Guest-os-2.12 _201208-02

A vendég operációs rendszer konfigurációs karakterlánca ugyanazokkal az adatokkal van beágyazva, valamint egy olyan dátummal, amely azt mutatja, hogy mely MSRC-javítások lettek figyelembe véve a kiadáshoz. Ebben a példában a Windows Server 2008 R2 rendszerhez készült MSRC-javítások, köztük az augusztus 2012. A rendszer csak a Windows Server adott verziójára vonatkozó javításokat tartalmazza. Ha például egy MSRC-javítás a Microsoft Officera vonatkozik, nem kerül bele, mert a termék nem része a Windows Server alaprendszerképének.

## <a name="guest-os-system-update-process"></a>Vendég operációs rendszer frissítési folyamata
Ezen az oldalon a vendég operációs rendszerek közelgő kiadásaival kapcsolatos információk találhatók. Az ügyfelek jelezték, hogy a kiadás bekövetkezésekor tudni szeretnék, mert a felhőalapú szolgáltatás szerepkörei újraindulnak, ha az automatikus frissítésre vannak beállítva. A vendég operációs rendszer kiadásai általában 2-3 héttel a MSRC frissítési kiadása után történnek, amely minden hónap második keddjén történik. Az új kiadások közé tartozik az összes vendég operációsrendszer-családhoz tartozó MSRC-javítás.

Microsoft Azure folyamatosan felszabadítja a frissítéseket. A vendég operációs rendszer csak egy ilyen frissítés a folyamatban. A kiadást számos tényező befolyásolhatja. Az Azure emellett több százezer gépen fut. Ez azt jelenti, hogy a szerepkör (ek) újraindításakor nem lehet pontos dátumot és időpontot megadni. A limit vagy az idő újraindítására szolgáló csomagon dolgozunk.

A vendég operációs rendszer új kiadásának közzétételekor időt vehet igénybe, hogy teljes mértékben propagálja az Azure-t. Mivel a szolgáltatások frissülnek az új vendég operációs rendszerbe, a rendszer újraindította a frissítési tartományok tiszteletben tartását. Az "automatikus" frissítések használatára beállított szolgáltatások először a kiadást kapják meg. A frissítés után a Azure Portal a szolgáltatáshoz tartozó új vendég operációs rendszer verziója jelenik meg. Az újrakiadások ezen időszak alatt előfordulhatnak. Előfordulhat, hogy egyes verziók több idő alatt is üzembe helyezhetők, és az automatikus frissítési újraindítások nem fordulnak elő a hivatalos kiadási dátum után sok hétig. Miután elérhetővé vált egy vendég operációs rendszer, explicit módon kiválaszthatja az adott verziót a portálról vagy a konfigurációs fájlból.

A vendég és a gazdagép operációs rendszerének frissítéseivel kapcsolatos további információk a újraindítással és a mutatókkal foglalkozó témakörben olvashatók. a [szerepkör-példányok az operációs rendszer frissítései miatt újraindulnak][restarts]az MSDN blogbejegyzésben.

Ha manuálisan frissíti a vendég operációs rendszert, további információkért tekintse meg a [vendég operációs rendszer][retirepolicy] kivezetési szabályzatát.

## <a name="guest-os-supportability-and-retirement-policy"></a>A vendég operációs rendszer támogatása és a nyugdíjazási szabályzat
A vendég operációs rendszer támogatásának és a nyugdíjazási szabályzatnak a magyarázata [itt][retirepolicy]található.

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Vendég operációs rendszer frissítése RSS-hírcsatorna]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
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
