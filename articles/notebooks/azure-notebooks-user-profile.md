---
title: Felhasználói profil és az azonosító használható az Azure-jegyzetfüzetek használata
description: Hogyan hozhat létre, és a felhasználói profil és a felhasználói azonosító az Azure-jegyzetfüzetek használata kezelheti.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 1fddefeb2a54ae775a9016799ffff1963eab247e
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970147"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>A profil és a felhasználói azonosító az Azure-notebookokhoz

Azure notebookok hatékony, együttműködő címtéren belüli a felhasználói profil megadja a nyilvános kép mások számára:

[@no__t – 1An Azure Notebooks Profile lap](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

A felhasználói azonosító az URL-címeket, projektek és jegyzetfüzetek megosztási részét képezi. Az alábbi lista ismerteti a különböző URL-cím-minták:

- `https://notebooks.azure.com/<user_id>`: A profil oldala.
- `https://notebooks.azure.com/<user_id>/projects`: Az Ön projektjei. Láthatja az összes projekt; más felhasználók csak a nyilvános projektek megtekintéséhez.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Projektfájlok.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Egy adott projekt klónozása.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Egy adott jegyzetfüzet vagy fájl HTML-előnézete.

## <a name="your-user-id"></a>A felhasználói azonosító

Amikor első alkalommal bejelentkezik az Azure-jegyzetfüzetek, a fiók automatikusan kap egy ideiglenes felhasználói Azonosítóját, például a "névtelen-idr3ca". Mindaddig, amíg felhasználói azonosítója, amely a következővel kezdődik: "névtelen-", Azure notebookok felszólítja, hogy módosítsa bejelentkezés:

![Rákérdezés a felhasználói azonosító létrehozása, amikor bejelentkezik az Azure-jegyzetfüzetek](media/accounts/create-user-id.png)

A **konfigurálása a felhasználói azonosító** parancsot is az ideiglenes felhasználó neve mellett jelenik meg:

![Konfigurálja a felhasználói azonosító parancs jelenik meg, amikor egy ideiglenes azonosító használata](media/accounts/configure-user-id-command.png)

A profil lapon bármikor is módosíthatja a felhasználói Azonosítóját.

A felhasználói AZONOSÍTÓnak négy és 16 karakterből, számból és kötőjelből kell állnia. Más karaktereket nem engedélyezettek, valamint a felhasználói azonosító nem kezdődhet és nem végződhet kötőjellel, és nem kötőjelet használhatja a sor. Mivel a felhasználói azonosítók minden Azure Notebooks-fiókban egyediek, a "felhasználói azonosító már használatban van" üzenet jelenik meg. (Az üzenet akkor is megjelenik, ha a Microsoft védjegyet felhasználói AZONOSÍTÓként kísérli meg használni.) Ezekben az esetekben válasszon másik felhasználói azonosítót.

> [!Important]
> Az azonosító módosítását érvényteleníti semmilyen URL, előfordulhat, hogy megosztott a korábbi azonosítójával Vissza az előző azonosító kísérelje meg újra érvényesítését a hivatkozások a módosíthatja az azonosítója. Azonban egy másik felhasználó egy nem használt jogcím addig azonosítója.

## <a name="your-profile"></a>A profil

A profil áll, az URL-cím nyilvánosan megtekinthető információk `https://notebooks.azure.com/<user_id>`. A profil lapon is megjeleníti a legutóbb használt és minden csillaggal megjelölt projektek.

A profil szerkesztéséhez használja a **profiladatok szerkesztése** parancsot a profil lapon. A szakaszok a profil a következők:

| Section | Tartalom |
| --- | --- |
| Profilkép | Kép a profil lapon jelenik meg. |
| Fiókinformáció | A megjelenített név, a felhasználói azonosító és a nyilvános e-mail-fiókot. Itt az e-mail-fiók más felhasználók mean Önnel a kapcsolatot biztosít, és eltérő lehet a [fiók](azure-notebooks-user-account.md) jelentkezzen be a saját maga az Azure-jegyzetfüzetek használatával. |
| Profiladatok | A hely, vállalati, beosztás, webhely és saját kezűleg rövid leírását. |
| Közösségi profilok | A GItHub, a Twitter és a Facebook-azonosítóval, ha megosztja őket. |
| Adatvédelmi beállítások | Itt két parancsot:<ul><li>**Saját profil exportálása**: létrehoz és letölt egy *.zip* fájlt, amely tartalmazza a szükséges információkat, az Azure-jegyzetfüzetek menti a profilját, beleértve a fényképet, profiladatokat és biztonsági naplókat.</li><li>**Fiók törlése**: Véglegesen törli a Azure Notebooksban tárolt összes személyes adatot.</li></ul> |
| Webhely-szolgáltatások engedélyezése | Az Azure-jegyzetfüzetek viselkedését aspektusa teszi lehetővé:<ul><li>**Egységes Előtérrendszer notebookok**: lehetővé teszi, hogy gyorsabban notebook indítási és jobb adatmegőrzés.</li><li>**A JupyterLab alapértelmezett futtatása**: Alapértelmezés szerint a Azure Notebooks a legtöbb felhasználó számára megfelelő egyszerű felhasználói felületet biztosít. JupyterLab gazdagabb azonban bonyolultabb felületet biztosít a tapasztalt felhasználók számára.</li><li>**VNext webhely**: lehetővé teszi, hogy a modernebb webes elrendezésben jelenik meg ebben a dokumentációban.</li></ul> |

## <a name="next-steps"></a>További lépések  

> [!div class="nextstepaction"]
> [Oktatóanyag: egy Futtatás ehhez lineáris regressziós modell futtatása Jupyter notebook létrehozása](tutorial-create-run-jupyter-notebook.md)
