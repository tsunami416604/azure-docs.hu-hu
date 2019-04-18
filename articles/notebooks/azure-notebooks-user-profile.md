---
title: Felhasználói profil és az azonosító használható az Azure-jegyzetfüzetek használata
description: Hogyan hozhat létre, és a felhasználói profil és a felhasználói azonosító az Azure-jegyzetfüzetek használata kezelheti.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: b8c21b908ca9162a7e44c7af1e222babc6ee1eb7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265610"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>A profil és a felhasználói azonosító az Azure-notebookokhoz

Azure notebookok hatékony, együttműködő címtéren belüli a felhasználói profil megadja a nyilvános kép mások számára:

[![Egy Azure-jegyzetfüzetek profillapján](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

A felhasználói azonosító az URL-címeket, projektek és jegyzetfüzetek megosztási részét képezi. Az alábbi lista ismerteti a különböző URL-cím-minták:

- `https://notebooks.azure.com/<user_id>`: A profil lapon.
- `https://notebooks.azure.com/<user_id>/projects`: A projektek. Láthatja az összes projekt; más felhasználók csak a nyilvános projektek megtekintéséhez.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Soubory projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Egy adott projektek klónok.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: A fájlok meghatározott jegyzetfüzet és HTML-előnézetet.

## <a name="your-user-id"></a>A felhasználói azonosító

Amikor első alkalommal bejelentkezik az Azure-jegyzetfüzetek, a fiók automatikusan kap egy ideiglenes felhasználói Azonosítóját, például a "névtelen-idr3ca". Mindaddig, amíg felhasználói azonosítója, amely a következővel kezdődik: "névtelen-", Azure notebookok felszólítja, hogy módosítsa bejelentkezés:

![Rákérdezés a felhasználói azonosító létrehozása, amikor bejelentkezik az Azure-jegyzetfüzetek](media/accounts/create-user-id.png)

A **konfigurálása a felhasználói azonosító** parancsot is az ideiglenes felhasználó neve mellett jelenik meg:

![Konfigurálja a felhasználói azonosító parancs jelenik meg, amikor egy ideiglenes azonosító használata](media/accounts/configure-user-id-command.png)

A profil lapon bármikor is módosíthatja a felhasználói Azonosítóját.

Felhasználói azonosító négy és tizenhat betűket, számokat és kötőjeleket tartalmazhat között kell állniuk. Más karaktereket nem engedélyezettek, valamint a felhasználói azonosító nem kezdődhet és nem végződhet kötőjellel, és nem kötőjelet használhatja a sor. Mivel a felhasználói azonosítók egyediek legyenek az összes Azure-jegyzetfüzetek fiókok, az üzenet jelenhet meg, a "Felhasználói azonosító már használatban van." (Az üzenet is megjelenik, ha próbálja meg használni, a Microsoft védjegyekre, a felhasználói azonosítóját.) Ezekben az esetekben válassza ki a különböző felhasználói azonosítóját.

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
| Adatvédelmi beállítások | Itt két parancsot:<ul><li>**Saját profil exportálása**: létrehoz és letölt egy *.zip* fájlt, amely tartalmazza a szükséges információkat, az Azure-jegyzetfüzetek menti a profilját, beleértve a fényképet, profiladatokat és biztonsági naplókat.</li><li>**Saját fiók törlése**: Véglegesen törli az összes Azure jegyzetfüzetekben tárolt személyes adatokat.</li></ul> |
| Webhely-szolgáltatások engedélyezése | Az Azure-jegyzetfüzetek viselkedését aspektusa teszi lehetővé:<ul><li>**Egységes Előtérrendszer notebookok**: lehetővé teszi, hogy gyorsabban notebook indítási és jobb adatmegőrzés.</li><li>**Alapértelmezés szerint a JupyterLab futnak**: Alapértelmezés szerint az Azure-jegyzetfüzetek, amely lehetővé teszi a felhasználók többsége számára egyszerű felhasználói felületet biztosít. JupyterLab gazdagabb azonban bonyolultabb felületet biztosít a tapasztalt felhasználók számára.</li><li>**VNext webhely**: lehetővé teszi, hogy a modernebb webes elrendezésben jelenik meg ebben a dokumentációban.</li></ul> |

## <a name="next-steps"></a>További lépések  

> [!div class="nextstepaction"]
> [Oktatóanyag: egy Futtatás ehhez lineáris regressziós modell futtatása Jupyter notebook létrehozása](tutorial-create-run-jupyter-notebook.md)
