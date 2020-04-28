---
title: Felhasználói profil és azonosító Azure Notebooks előzetes verzióval való használatra
description: Felhasználói profil és felhasználói azonosító létrehozása és kezelése Azure Notebooks használatával, amely a megosztott jegyzetfüzetek URL-címének részévé válik.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646279"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Azure Notebooks előzetes verziójának profilja és felhasználói azonosítója

A Azure Notebooks hatékony, együttműködésen alapuló területén a felhasználói profil a következő nyilvános képet jeleníti meg mások számára:

[![Azure Notebooks profil oldala](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

A felhasználói azonosító a projektek és jegyzetfüzetek megosztásához használt URL-címek részét képezi. Az alábbi lista a különböző URL-mintákat ismerteti:

- `https://notebooks.azure.com/<user_id>`: A profil oldala.
- `https://notebooks.azure.com/<user_id>/projects`: Az Ön projektjei. Megjelenik az összes projekt; a többi felhasználó csak a nyilvános projekteket látja.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Projektfájlok.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Egy adott projekt klónozása.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Egy adott jegyzetfüzet vagy fájl HTML-előnézete.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>Felhasználói azonosító

Amikor első alkalommal jelentkezik be Azure Notebooksba, a fiók automatikusan egy ideiglenes felhasználói azonosítót kap, például: "Anon-idr3ca". Ha olyan felhasználói AZONOSÍTÓval rendelkezik, amely "Anon-" karakterrel kezdődik, Azure Notebooks kéri, hogy minden bejelentkezéskor módosítsa a következőt:

![Felhasználói azonosító létrehozása a Azure Notebooksba való bejelentkezéskor](media/accounts/create-user-id.png)

A **felhasználói azonosító konfigurálása** parancs az ideiglenes Felhasználónév mellett is megjelenik:

![A felhasználói azonosító parancs konfigurálása, amely akkor jelenik meg, ha ideiglenes azonosítót használ](media/accounts/configure-user-id-command.png)

A felhasználói azonosítót bármikor módosíthatja a profil oldalán.

A felhasználói AZONOSÍTÓnak négy és 16 karakterből, számból és kötőjelből kell állnia. Nem engedélyezettek más karakterek, és a felhasználói azonosító nem kezdődhet vagy végződhet kötőjellel, vagy egy sorban több kötőjelet is használhat. Mivel a felhasználói azonosítók minden Azure Notebooks-fiókban egyediek, a "felhasználói azonosító már használatban van" üzenet jelenik meg. (Az üzenet akkor is megjelenik, ha a Microsoft védjegyet felhasználói AZONOSÍTÓként kísérli meg használni.) Ezekben az esetekben válasszon másik felhasználói azonosítót.

> [!Important]
> Az azonosító módosítása érvényteleníti azokat az URL-címeket, amelyeket az előző azonosító alapján megosztott. Az AZONOSÍTÓját visszaállíthatja az előző AZONOSÍTÓra a hivatkozások újbóli ellenőrzéséhez. Ugyanakkor lehetséges, hogy egy másik felhasználó is igénybe szeretné venni az azonosító használatát.

## <a name="your-profile"></a>Saját profil

A profil nyilvánosan megtekinthető információkból áll az URL- `https://notebooks.azure.com/<user_id>`címen. A profil oldala a közelmúltban használt projekteket és bármely csillaggal elválasztott projektet is megjeleníti.

A profil szerkesztéséhez használja a profil- **információ szerkesztése** parancsot a profil oldalán. A profil részei a következők:

| Section | Tartalom |
| --- | --- |
| Profil fényképe | A profil oldalán megjelenő rendszerkép. |
| Fiókadatok | A megjelenítendő név, a felhasználói azonosító és a nyilvános e-mail-fiók. Az itt található e-mail-fiók más felhasználók számára is lehetővé teszi, hogy kapcsolatba lépjen Önnel, és eltérhet a Azure Notebooksba való bejelentkezéshez használt [fióktól](azure-notebooks-user-account.md) . |
| Profil adatai | A hely, a vállalat, a beosztás, a webhely és a rövid leírása. |
| Közösségi profilok | Ha meg szeretné osztani a GItHub-, Twitter-és Facebook-azonosítókat. |
| Adatvédelmi beállítások | Két parancsot biztosít:<ul><li>**Saját profil exportálása**: létrehoz és letölt egy *. zip* fájlt, amely tartalmazza az összes olyan információt, amelyet Azure Notebooks a profilba ment, beleértve a fényképet, a profil adatait és a biztonsági naplókat.</li><li>**Fiók törlése**: a Azure Notebooksban tárolt összes személyes adat véglegesen törlődik.</li></ul> |
| Hely funkcióinak engedélyezése | Lehetővé teszi a Azure Notebooks viselkedési szempontjainak szabályozását:<ul><li>**Egységes frontend jegyzetfüzetekhez: a**jegyzetfüzetek gyorsabb indítását és nagyobb adatmegőrzést tesz lehetővé.</li><li>**A JupyterLab alapértelmezett futtatása**: alapértelmezés szerint a Azure Notebooks a legtöbb felhasználó számára megfelelő egyszerű felhasználói felületet biztosít. A JupyterLab sokoldalú, de bonyolultabb felületet biztosít a tapasztalt felhasználók számára.</li><li>**VNext webhelye**: lehetővé teszi a dokumentációban látható, korszerű webes elrendezést.</li></ul> |

## <a name="next-steps"></a>További lépések  

> [!div class="nextstepaction"]
> [Oktatóanyag: Jupyter-jegyzetfüzet futtatásának létrehozása lineáris regresszióhoz](tutorial-create-run-jupyter-notebook.md)
