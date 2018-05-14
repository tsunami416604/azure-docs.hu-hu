---
title: Hozzon létre, és tegye közzé a Piactéri elemet Azure verem |} Microsoft Docs
description: Hozzon létre, és tegye közzé a Piactéri elemet Azure-készletben.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5e0349d6bae9295e7a0ba9f366f84753ebd838c2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="create-and-publish-a-marketplace-item"></a>Piactéri termék létrehozása és közzététele

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

## <a name="create-a-marketplace-item"></a>Piactéri elem létrehozása
1. [Töltse le](http://www.aka.ms/azurestackmarketplaceitem) az Azure-gyűjtemény csomagoló eszköz és a minta Azure verem Piactéri elemet.
2. Nyissa meg a minta Piactéri elemet, és nevezze át a **SimpleVMTemplate** mappa. (Az azonos név használata az a Piactéri elemet – például **Contoso.TodoList**.) Ez a mappa tartalmaz:
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Hozzon létre egy Azure Resource Manager sablon](../azure-resource-manager/resource-group-authoring-templates.md) , vagy válasszon egy sablont a Githubból. A Piactéri elemet a sablon használatával hozható létre erőforrás.
4. Győződjön meg arról, hogy az erőforrás sikeresen telepíthető-e, tesztelje a sablon a Microsoft Azure verem API-khoz.
5. Ha a sablont a virtuálisgép-lemezkép támaszkodik, kövesse az utasításokat [egy virtuálisgép-lemezkép hozzáadása Azure verem](azure-stack-add-vm-image.md).
6. Az Azure Resource Manager sablon mentése az a **/Contoso.TodoList/DeploymentTemplates/** mappa.
7. Válassza ki az ikonok és a szöveg a Piactéri elemet. Ikonok hozzáadása a **ikonok** mappa, és adja hozzá a szöveget a **erőforrások** fájlt a **karakterláncok** mappát. A kis, közepes, Large és Wide elnevezési konvenciót használ ikonok. Lásd: [Piactéri elemet felhasználói felület hivatkozás](#reference-marketplace-item-ui) részletes leírását.
   
   > [!NOTE]
   > Négy ikon különböző méretű (kis, közepes, nagy, széles) szükség a Piactéri elemet megfelelően készítéséhez.
   > 
   > 
8. Az a **manifest.json** fájl, módosítsa **neve** nevére, a Piactéri elemet. Is módosíthatja **publisher** a nevét vagy a vállalat.
9. A **összetevők**, módosítsa **neve** és **elérési** az Azure Resource Manager sablon foglalja a megfelelő információkkal történő.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Cserélje le **saját Piactéri elemek** a kategóriák, ahol a Piactéri elemet meg kell jelennie az listáját.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Bármely további módosításainak manifest.json, lásd [hivatkozás: piactér elem manifest.json](#reference-marketplace-item-manifestjson).
12. A mappák csomagolni .azpkg fájlként, nyisson meg egy parancssort, és futtassa a következő parancsot:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > A kimeneti csomag teljes elérési útja létezzen. Például ha a kimeneti elérési út C:\MarketPlaceItem\yourpackage.azpkg, a mappa C:\MarketPlaceItem léteznie kell.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Piactéri elem közzététele
1. A Piactéri elemet (.azpkg) feltöltése az Azure Blob storage használata a PowerShell vagy Azure Tártallózó. Töltse fel a helyi Azure verem tárterület, vagy töltse fel az Azure-tárhelyre. (A csomag egy ideiglenes helyre szó.) Győződjön meg arról, hogy a blob nyilvánosan elérhető-e.
2. Az ügyfél virtuális gépen a Microsoft Azure verem környezetben győződjön meg arról, hogy a PowerShell-munkamenethez be van állítva a szolgáltatás rendszergazdai hitelesítő adataival. Hogyan hitelesítheti az Azure-készletben PowerShell utasításokat található [a PowerShell-lel egy sablon telepítésének](user/azure-stack-deploy-template-powershell.md).
3. Használja a **Add-AzureRMGalleryItem** PowerShell-parancsmag használatával a Piactéri elemet közzétételéhez Azure verem. Példa:
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Paraméter | Leírás |
   | --- | --- |
   | SubscriptionID |Felügyeleti előfizetés-azonosító. Ez a PowerShell használatával kérheti le. Ha inkább a portál eléréséhez, nyissa meg a szolgáltató előfizetés, és másolja az előfizetés-azonosító. |
   | GalleryItemUri |A gyűjtemény csomagot, amely már fel lett töltve tárolási URI BLOB. |
   | Apiversion |Állítja be **2015-04-01**. |
4. Navigáljon a portálon. A Piactéri elemet a portálon--operátor vagy a felhasználó láthatja.
   
   > [!NOTE]
   > A csomag jelenik meg több percig is eltarthat.
   > 
   > 
5. A Piactéri elemet már korábban mentett verem Azure piactérről. Ha szeretné törölni az a Blob-tároló helyéhez.
6. A Piactéri elemet eltávolításához használja a **Remove-AzureRMGalleryItem** parancsmag. Példa:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > A piactér felhasználói felület hiba jelenhet meg elem eltávolítása után. A hiba javításához kattintson **beállítások** a portálon. Ezt követően válassza **elveti a módosításokat** alatt **Portal testreszabási**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>– Referencia: Piactér-elem manifest.json
### <a name="identity-information"></a>Azonosító adatok
| Name (Név) | Szükséges | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| Name (Név) |X |Karakterlánc |[A-Za-z0-9]+ | |
| Közzétevő |X |Karakterlánc |[A-Za-z0-9]+ | |
| Verzió |X |Karakterlánc |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metaadatok
| Name (Név) | Szükséges | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| Megjelenítendő név |X |Karakterlánc |A javaslat 80 karakter |A portál nem jelenik meg az elem neve szabályosan Ha hosszabb 80 karakternél. |
| PublisherDisplayName |X |Karakterlánc |A javaslat 30 karakternél |A portál nem jelenik meg a kiadó nevét szabályosan ha 30 karakternél hosszabb. |
| PublisherLegalName |X |Karakterlánc |Legfeljebb 256 karakter hosszú lehet | |
| Összegzés |X |Karakterlánc |60 és 100 karakter | |
| LongSummary |X |Karakterlánc |140 és 256 karakter |Még nincs Azure-készletben. |
| Leírás |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 – 5000 karakter | |

### <a name="images"></a>Képek
A piactér az alábbi ikonok:

| Name (Név) | Szélesség | Magasság | Megjegyzések |
| --- | --- | --- | --- |
| Széles |255 px |115 px |Mindig szükséges. |
| Nagy |115 px |115 px |Mindig szükséges. |
| Közepes |90 képpont |90 képpont |Mindig szükséges. |
| Kicsi |40 px |40 px |Mindig szükséges. |
| Képernyőfelvétel |533 képpont |32 px |Optional |

### <a name="categories"></a>Kategóriák
Minden egyes Piactéri elemet kell címkézését egy kategóriát, amely azonosítja a portál felhasználói felületi elem helyére. Azure verem a meglévő kategóriák közül választhat (számítási, adatok + tárolás, stb.), vagy válasszon egy újat.

### <a name="links"></a>Hivatkozások
Minden egyes Piactéri elemet tartalmazhat különböző, a tartalmakra mutató hivatkozásokat tartalmaz. A hivatkozások vannak megadva a nevek és URI-azonosítók listáját.

| Name (Név) | Szükséges | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| Megjelenítendő név |X |Karakterlánc |Legfeljebb 64 karakter hosszú lehet | |
| URI |X |URI | | |

### <a name="additional-properties"></a>További tulajdonságok
A fenti metaadatok mellett piactér szerzők biztosíthat egyéni kulcs/érték pár adatokat a következő formában:

| Name (Név) | Szükséges | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| Megjelenítendő név |X |Karakterlánc |Legfeljebb 25 karakter hosszú lehet | |
| Érték |X |Karakterlánc |Legfeljebb 30 karakterből | |

### <a name="html-sanitization"></a>HTML tisztítási
Valamelyik mezőben, amely lehetővé teszi a HTML a következő elemek és attribútumok használhatók:

H1, h2, h3, h4, h5, p, ol, ul, li, a [cél |} href], br, erős, em, a b kiszolgálóra, és i

## <a name="reference-marketplace-item-ui"></a>Útmutató: Piactéri elemet felhasználói felület
Ikonok és Piactéri elemek, mint a verem Azure-portálon szövege a következők.

### <a name="create-blade"></a>A Create (Létrehozás) panel
![A Create (Létrehozás) panel](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Piactér elem részleteit megjelenítő panelen
![Piactér elem részleteit megjelenítő panelen](media/azure-stack-marketplace-item-ui-reference/image3.png)

