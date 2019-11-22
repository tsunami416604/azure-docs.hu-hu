---
title: Felhasználói profil és az azonosító használható az Azure-jegyzetfüzetek használata
description: Felhasználói profil és felhasználói azonosító létrehozása és kezelése Azure Notebooks használatával, amely a megosztott jegyzetfüzetek URL-címének részévé válik.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 0874fe8223ae9c49ccfe4e8efedf2620117a0f8c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277613"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>A profil és a felhasználói azonosító az Azure-notebookokhoz

Azure notebookok hatékony, együttműködő címtéren belüli a felhasználói profil megadja a nyilvános kép mások számára:

[Azure Notebooks-profil oldalának ![](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

A felhasználói azonosító az URL-címeket, projektek és jegyzetfüzetek megosztási részét képezi. Az alábbi lista ismerteti a különböző URL-cím-minták:

- `https://notebooks.azure.com/<user_id>`: A profil lapon.
- `https://notebooks.azure.com/<user_id>/projects`: A projekteket. Láthatja az összes projekt; más felhasználók csak a nyilvános projektek megtekintéséhez.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Soubory projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Egy adott projektekhez klónok.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: A fájlok meghatározott jegyzetfüzet és HTML-előnézetet.

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
| Adatvédelmi beállítások | Itt két parancsot:<ul><li>**Saját profil exportálása**: létrehoz és letölt egy *.zip* fájlt, amely tartalmazza a szükséges információkat, az Azure-jegyzetfüzetek menti a profilját, beleértve a fényképet, profiladatokat és biztonsági naplókat.</li><li>**Saját fiók törlése**: véglegesen törli az összes Azure jegyzetfüzetekben tárolt személyes adatokat.</li></ul> |
| Webhely-szolgáltatások engedélyezése | Az Azure-jegyzetfüzetek viselkedését aspektusa teszi lehetővé:<ul><li>**Egységes Előtérrendszer notebookok**: lehetővé teszi, hogy gyorsabban notebook indítási és jobb adatmegőrzés.</li><li>**Alapértelmezés szerint a JupyterLab futnak**: alapértelmezés szerint az Azure-jegyzetfüzetek, amely lehetővé teszi a felhasználók többsége számára egyszerű felhasználói felületet biztosít. JupyterLab gazdagabb azonban bonyolultabb felületet biztosít a tapasztalt felhasználók számára.</li><li>**VNext webhely**: lehetővé teszi, hogy a modernebb webes elrendezésben jelenik meg ebben a dokumentációban.</li></ul> |

## <a name="next-steps"></a>Következő lépések  

> [!div class="nextstepaction"]
> [Oktatóanyag: egy Futtatás ehhez lineáris regressziós modell futtatása Jupyter notebook létrehozása](tutorial-create-run-jupyter-notebook.md)
