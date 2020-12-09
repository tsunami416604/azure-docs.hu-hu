---
title: Power BI bérlő regisztrálása és bevizsgálása (előzetes verzió)
description: Megtudhatja, hogyan regisztrálhat és vizsgálhat meg egy Power BI bérlőt az Azure hatáskörébe-portál használatával.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: ce23119e568347fff2ad270cfea7f8b563cae529
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904992"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Power BI bérlő regisztrálása és bevizsgálása (előzetes verzió)

Ez a cikk bemutatja, hogyan regisztrálhat és vizsgálhat meg egy Power BI bérlőt az Azure hatáskörébe-portál használatával.

> [!Note]
> Ha a hatáskörébe tartozó példány és a Power BI bérlő ugyanabban az Azure-bérlőben található, akkor csak a felügyelt identitás (MSI) hitelesítést használhatja egy Power BI bérlő vizsgálatának beállításához. Ha a hatáskörébe tartozó példány és Power BI bérlő különböző Azure-bérlőn található, akkor hitelesítenie kell magát a delegált hitelesítéssel, és az ellenőrzéseket a PowerShell használatával kell beállítania. Lásd: [a PowerShell használata a Power bi regisztrálásához és vizsgálatához](powershell-register-scan-power-bi.md).

## <a name="create-a-security-group-for-permissions"></a>Biztonsági csoport létrehozása engedélyekhez

A hitelesítés beállításához hozzon létre egy biztonsági csoportot, és adja hozzá a katalógus felügyelt identitását.

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory**.
1. Hozzon létre egy új biztonsági csoportot a Azure Active Directoryban, [és hozzon létre egy alapszintű csoportot, és vegyen fel tagokat a Azure Active Directory használatával](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt biztonsági csoporttal.

1. Válassza a **Biztonság** lehetőséget a **csoport típusaként**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Biztonsági csoport típusa":::

1. Adja hozzá a katalógus felügyelt identitását ehhez a biztonsági csoporthoz. Válassza a **tagok**, majd a **+ Tagok hozzáadása** elemet.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Adja hozzá a katalógus felügyelt példányát a csoporthoz.":::

1. Keresse meg a katalógust, és válassza ki.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Katalógus hozzáadása a kereséshez":::

    Ekkor megjelenik egy sikeres értesítés, amely azt mutatja, hogy hozzá lett adva.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="A katalógus MSI-fájljának hozzáadása sikeres":::

## <a name="associate-the-security-group-with-the-tenant"></a>A biztonsági csoport hozzárendelése a bérlőhöz

1. Jelentkezzen be a [Power bi felügyeleti portálra](https://app.powerbi.com/admin-portal/tenantSettings).

    > [!Important]
    > A bérlői beállítások lap megtekintéséhez Power BI rendszergazdának kell lennie.

1. A **fejlesztői beállítások** lehetőség kiválasztásával  >  **engedélyezheti az egyszerű szolgáltatásoknak a csak olvasási jogosultsággal rendelkező Power bi felügyeleti API-k (előzetes verzió) használatát**.
1. Válassza az **egyes biztonsági csoportok** lehetőséget.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Kép: az egyszerű Power BI felügyeleti API-engedélyek beolvasásának engedélyezése a szolgáltatásoknak":::

    > [!Caution]
    > Ha engedélyezi a létrehozott biztonsági csoportot (amelyet a saját adatkatalógus felügyelt identitása tagként használ) a csak olvasási jogosultsággal rendelkező Power BI felügyeleti API-k használatához, akkor azt is lehetővé teszi, hogy a rendszer a metaadatokat (például az irányítópultot, a jelentés nevét, a tulajdonosokat, a leírásokat stb.) a bérlő összes Power BI összetevője számára is hozzáférhessen. Miután behúzta a metaadatokat az Azure hatáskörébe, a hatáskörébe tartozó engedélyek, nem Power BI engedélyek, határozza meg, ki láthatja a metaadatokat.

    > [!Note]
    > A biztonsági csoportot eltávolíthatja a fejlesztői beállításokból, de a korábban kibontott metaadatokat a rendszer nem távolítja el a hatáskörébe tartozó fiókból. Ha kívánja, külön is törölheti.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>A Power BI regisztrálása és a vizsgálat beállítása

Most, hogy megadta a katalógus engedélyeit a Power BI bérlő felügyeleti API-hoz való kapcsolódáshoz, beállíthatja a vizsgálatot a katalógus-portálról.

Első lépésként adjon hozzá egy speciális funkció-jelölőt a hatáskörébe URL-címéhez 

1. Adja hozzá a következő karakterláncot a hatáskörébe tartozó példány URI-ja végéhez: `?feature.ext.catalog={"pbi":"true"}` . Ez engedélyezi a Power BI regisztrációs lehetőséget a katalógusban.

1. Válassza ki a **felügyeleti központ** ikonját.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Felügyeleti központ ikonja":::

1. Ezután válassza az **+ új** lehetőséget az **adatforrások** területen.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Új adatforrás gombjának képe":::

    Adatforrásként válassza a **Power bi** lehetőséget.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="A választható adatforrások listáját bemutató kép":::

1. Adjon egy rövid nevet a Power BI-példánynak.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Kép: Power BI adatforrás-felhasználóbarát név":::

    A névnek 3-63 karakter hosszúnak kell lennie, és csak betűket, számokat, aláhúzásokat és kötőjeleket tartalmazhat.  A szóközök nem engedélyezettek.

    Alapértelmezés szerint a rendszer megkeresi a Power BI bérlőt, amely ugyanabban az Azure-előfizetésben található.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI adatforrás regisztrálva":::

1. Adja meg a vizsgálat nevét. Figyelje meg, hogy az egyetlen támogatott hitelesítési módszer a **felügyelt identitás**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="A Power BI Scan telepítőjét ábrázoló kép":::

    A vizsgálat nevének 3-63 karakter hosszúnak kell lennie, és csak betűket, számokat, aláhúzásokat és kötőjeleket tartalmazhat.  A szóközök nem engedélyezettek.

1. Egy vizsgálati trigger beállítása. A lehetőségek **egyszer**, **7 naponta** és **30 naponként**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Trigger rendszerképének vizsgálata":::

1. Az **új vizsgálat áttekintése** lapon válassza a **Mentés és Futtatás** lehetőséget a vizsgálat elindításához.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Power BI képernyő rendszerképének mentése és futtatása":::

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan lehet PowerShell-parancsmagokkal regisztrálni és beolvasni egy Power BI bérlőt, tekintse meg a következőt:
  
- [A PowerShell használata a Power BI regisztrálásához és vizsgálatához](powershell-register-scan-power-bi.md)
