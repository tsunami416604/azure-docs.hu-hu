---
title: A Power BI munkaterületi gyűjtemények újdonságai
description: A Power BI munkaterületi gyűjtemények újdonságai a legfrissebb adatok lekérése
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 072fa8978bd4f08494191708d9a71c6c112a0629
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252053"
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>A Power BI munkaterületi gyűjtemények újdonságai

Frissítések **Power BI-Munkaterületcsoportok** rendszeresen jelennek meg. Azonban nem minden kiadás új szolgáltatásokkal rendelkező felhasználó; Néhány kiadásai háttérszolgáltatás képességek összpontosítanak. A Microsoft jelölje ki az új felhasználói képességek itt.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

## <a name="march-2017"></a>2017. március

**Önkiszolgáló képességek**

* [Új jelentés létrehozása](create-report-from-dataset.md)
* [A jelentés Mentés másként](save-reports.md)
* Jelentés beágyazása az új üzemmódba olvasás/Szerkesztés és létrehozás 
* [A jelentés szerkesztési/olvasási mód közötti átváltása](toggle-mode.md)

**Adatkapcsolat REST API-kkal**

* [Adatkészlet létrehozása](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Adatok leküldése 

**Felügyeleti API-k**

* Klónozott jelentés és adatkészlet
* A jelentés kötés egy másik adatkészlethez

**Példák**

* Frissített [jelentés JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>2016. december

* [Új JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>2016. október

* [Bővített analitika R és a Power BI munkaterületi gyűjtemények](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>2016. augusztus 31-ig.
Ez a kiadás tartalmazza:

* Minden új JavaScript SDK-t támogató [speciális szűrési és a lap navigációs](interact-with-reports.md).
* Power BI-Munkaterületcsoportok mostantól támogatottak az adatközpontban közép-Kanada. Ellenőrizze [adatközpont állapot](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>2016. július 11.
Ez a kiadás tartalmazza:

* **Nagyszerű hírünk van!** A Power BI munkaterületi gyűjtemények szolgáltatás már nem előzetes verzió – a mostantól általánosan elérhető (az általánosan elérhető).  
* Minden REST API-k helyeztünk át **/beta** való **/v1.0**.
* .NET-keretrendszer és a JavaScript SDK-k frissült **1.0-s verziójú**.
* Power BI API-hívások most már közvetlenül az API-kulcsok segítségével hitelesíthetők. Alkalmazás-jogkivonatokról csak beágyazásához szükséges. Ennek részeként az 1.0-s verziójú API-k üzembe helyezése és fejlesztési tokenek elavultak, de azok továbbra is dolgozhat a bétaverzió a 2016. December 30-ig. További tudnivalókért lásd: [hitelesítés és engedélyezés a Power BI-Munkaterületcsoportok](app-token-flow.md).
* Sorszintű biztonság (RLS) támogatása alkalmazás-jogkivonatokról és beágyazott jelentéseket. További tudnivalókért lásd: [sorszintű biztonság a Power BI-Munkaterületcsoportok](row-level-security.md).
* Frissített mintaalkalmazás összes **1.0-s verziójú** API-hívások.
* A Power BI-Munkaterületcsoportok Azure SDK-t, a PowerShell és CLI támogatása.
* Felhasználók Vizualizáció adatokat is exportálhat egy **.csv**.
* A Power BI-Munkaterületcsoportok összes azonos nyelvű vagy területi beállítás a Microsoft Azure mostantól támogatottak. További tudnivalókért lásd: [Azure - nyelvek](https://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

