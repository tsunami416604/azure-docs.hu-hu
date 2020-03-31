---
title: Felhasználói profil és azonosító az Azure Notebookelőzetes verzióhoz
description: Hogyan hozhat létre és kezelhet felhasználói profilját és felhasználói azonosítóját az Azure Notebooks segítségével, amely a megosztott jegyzetfüzetek URL-címének részévé válik.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646279"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Az Azure Notebookok előzetes verziójának profilja és felhasználói azonosítója

Az Azure Notebookok hatékony, együttműködési területén belül a felhasználói profil bemutatja a nyilvános képet másoknak:

[![Az Azure Notebooks profillapja](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

A felhasználói azonosító a projektek és jegyzetfüzetek megosztásához használt URL-ek része. Az alábbi lista a különböző URL-mintákat ismerteti:

- `https://notebooks.azure.com/<user_id>`: A profiloldalad.
- `https://notebooks.azure.com/<user_id>/projects`: A projektek. Az összes projektet láthatja; más felhasználók csak a nyilvános projekteket látják.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Projektfájlok.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Egy adott projekt klónozása.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Egy adott jegyzetfüzet vagy fájl HTML-előnézete.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>Felhasználói azonosító

Amikor először jelentkezik be az Azure Notebooksba, a fiók automatikusan hozzárendel egy ideiglenes felhasználói azonosítót, például "anon-idr3ca". Mindaddig, amíg rendelkezik egy "anon-" szolgáltatással kezdődő felhasználói azonosítóval, az Azure Notebooks kéri, hogy módosítsa azt, amikor bejelentkezik:

![Az Azure-jegyzetfüzetekbe való bejelentkezéskor a felhasználói azonosító létrehozásának kérése](media/accounts/create-user-id.png)

A **Felhasználói azonosító konfigurálása** parancs az ideiglenes felhasználónév mellett is megjelenik:

![Ideiglenes azonosító használata esetén megjelenő Felhasználói azonosító konfigurálása parancs](media/accounts/configure-user-id-command.png)

A profiloldalon bármikor módosíthatja a felhasználói azonosítóját.

A felhasználói azonosítónak négy és tizenhat betű, szám és kötőjel között kell lennie. Más karakterek nem engedélyezettek, és a felhasználói azonosító nem kezdődhet vagy végződhet kötőjellel, illetve nem használhat több kötőjelet egymás után. Mivel a felhasználói azonosítók az összes Azure Notebook-fiókban egyediek, a következő üzenet jelenhet meg: "A felhasználói azonosító már használatban van." (Az üzenet akkor is megjelenik, ha egy Microsoft védjegyet próbál felhasználói azonosítóként használni.) Ezekben az esetekben válasszon másik felhasználói azonosítót.

> [!Important]
> Az azonosító módosítása érvényteleníti az előző azonosítóval megosztott URL-eket. A hivatkozások újraérvényesítéséhez módosíthatja az azonosítóját az előző azonosítóra. Azonban lehetséges, hogy egy másik felhasználó igényelegy nem használt azonosítót időközben.

## <a name="your-profile"></a>Az Ön profilja

A profil az URL-címen látható, nyilvánosan megtekinthető információkból `https://notebooks.azure.com/<user_id>`áll. A profiloldalad a legutóbb használt projekteket és a csillaggal megjelölt projekteket is megjeleníti.

A profil szerkesztéséhez használja a **Profiladatok szerkesztése** parancsot a profillapon. A profilod szakaszai a következők:

| Section | Tartalom |
| --- | --- |
| Profil fotó | A profiloldalon megjelenő kép. |
| Fiók adatai | A megjelenítendő név, a felhasználói azonosító és a nyilvános e-mail fiók. Az e-mail fiók itt biztosít más felhasználók számára a kapcsolatot, és eltérhet a [fiók,](azure-notebooks-user-account.md) amelyet az Azure Notebooks-ba való bejelentkezéshez használ. |
| Profil adatai | Az Ön tartózkodási helye, cége, beosztása, webhelye és önmagáról szóló rövid leírás. |
| Közösségi profilok | A GItHub, a Twitter és a Facebook azonosítói, ha meg szeretné osztani őket. |
| Adatvédelmi beállítások | Két parancsot tartalmaz:<ul><li>**Saját profil exportálása:** létrehoz és letölt egy *.zip* fájlt, amely tartalmazza az Azure Notebooks által a profiljába mentett összes információt, beleértve a fényképet, a profiladatokat és a biztonsági naplókat.</li><li>**Saját fiók törlése:** Véglegesen törli az Azure Notebookokban tárolt összes személyes adatot.</li></ul> |
| Webhelyszolgáltatások engedélyezése | Lehetővé teszi az Azure-jegyzetfüzetek viselkedésének szempontjait:<ul><li>**Egyesített előtér a notebookokhoz:** gyorsabb notebook indítást és jobb adatmegőrzést tesz lehetővé.</li><li>**Futtatás a JupyterLab alapértelmezés szerint:** Alapértelmezés szerint az Azure Notebooks egy egyszerű felhasználói felületet biztosít, amely a legtöbb felhasználó számára megfelelő. JupyterLab nyújt gazdagabb, de bonyolultabb felület a tapasztalt felhasználók számára.</li><li>**VNext Weboldal**: lehetővé teszi a modernizált webes elrendezés látható ebben a dokumentációban.</li></ul> |

## <a name="next-steps"></a>További lépések  

> [!div class="nextstepaction"]
> [Oktatóanyag: hozzon létre egy Jupyter notebookot a lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
