---
title: Azure AD Connect-adatbázis áthelyezése SQL Server Expressről SQL Serverre | Microsoft Docs
description: Ez a dokumentum azt mutatja be, hogyan helyezhető át az Azure AD Connect-adatbázis a helyi SQL Server Expressről egy távoli SQL Serverre.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 372470ffb1c296ee30702a2df0d8e9d00480a492
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55485228"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Azure AD Connect-adatbázis áthelyezése SQL Server Expressről SQL Serverre 

Ez a dokumentum azt mutatja be, hogyan helyezhető át az Azure AD Connect-adatbázis a helyi SQL Server Expressről egy távoli SQL Serverre.  A feladat az alább ismertetett eljárásokkal hajtható végre.

## <a name="about-this-scenario"></a>A forgatókönyv ismertetése
Szeretnénk néhány információval kezdeni, amelyek erre a forgatókönyvre vonatkoznak.  Az Azure AD Connect 1.1.819.0-s verziója van telepítve egy önálló Windows Server 2016 tartományvezérlőn,  amely az adatbázishoz a beépített SQL Server 2012 Express Editiont használja.  Az adatbázis egy SQL Server 2017 kiszolgálóra lesz áthelyezve.

![](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Az Azure AD Connect-adatbázis áthelyezése
Az alábbi lépésekkel áthelyezheti az Azure AD Connect-adatbázist egy távoli SQL Serverre.

1.  Az Azure AD Connect-kiszolgálón lépjen a **Szolgáltatások** részhez, és állítsa le a **Microsoft Azure AD-szinkronizáló** szolgáltatást.
2. Keresse meg a **% Program Files%\Microsoft az Azure AD szinkronizálása/Data/** mappát, és átmásolja a **ADSync.mdf** és **ADSync_log.ldf** a távoli SQL Server-fájlok.
3. Indítsa újra a **Microsoft Azure AD-szinkronizáló** szolgáltatást az Azure AD Connect-kiszolgálón.
4. Az Azure AD Connect eltávolításához lépjen a következő helyre: Vezérlőpult – Programok – Programok és szolgáltatások.  Válassza ki a Microsoft Azure AD Connectet, és kattintson a fent található eltávolítási gombra.
5. A távoli SQL Serveren nyissa meg az SQL Server Management Studio alkalmazást.
6. Kattintson a jobb gombbal a Databases (Adatbázisok) elemre, majd válassza ki az Attach (Csatolás) lehetőséget.
7. Az **Attach Databases** (Adatbázisok csatolása) képernyőn kattintson az **Add** (Hozzáadás) gombra, és keresse meg az ADSync.mdf fájlt.  Kattintson az **OK** gombra.
![](media/how-to-connect-install-move-db/move2.png)

8. Az adatbázis csatolása után lépjen vissza az Azure AD Connect-kiszolgálóhoz, és telepítse az Azure AD Connectet.
9. Miután az MSI-telepítés befejeződött, az Azure AD Connect varázslója elindítja az expressz módú telepítést. A Kilépés ikonra kattintva zárja be a képernyőt.
![Üdvözlőképernyő](./media/how-to-connect-install-move-db/db1.png)
10. Indítson új parancssort vagy PowerShell-munkamenetet. Lépjen a <drive>\program files\Microsoft Azure AD Connect mappához. Az Azure AD Connect-varázsló meglévő adatbázist használó módban való elindításához futtassa az .\AzureADConnect.exe /useexistingdatabase parancsot.
![PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. Megjelenik az Azure AD Connect üdvözlőképernyője. A licencfeltételek és az adatvédelmi nyilatkozat elfogadása után kattintson a **Folytatás** gombra.
![Üdvözlőképernyő](./media/how-to-connect-install-move-db/db3.png)
12. **A szükséges összetevők telepítése** képernyőn a **Meglévő SQL Server használata** lehetőség engedélyezve van. Adja meg az ADSync-adatbázist futtató SQL Server nevét. Ha az ADSync-adatbázist futtató SQL-motorpéldány nem az alapértelmezett példány az SQL Serveren, meg kell adnia az SQL-motorpéldány nevét. Ha az SQL-böngészés nincs engedélyezve, meg kell adnia az SQL-motorpéldány portszámát is. Példa:         
![Üdvözlőképernyő](./media/how-to-connect-install-move-db/db4.png)           

13. A **Azure AD-hez való csatlakozásra** szolgáló képernyőn meg kell adnia az Azure AD-címtár globális rendszergazdájának hitelesítő adatait. Javasoljuk, hogy az alapértelmezett onmicrosoft.com tartományban található fiókot használjon. Ez a fiók kizárólag egy Azure AD-szolgáltatásfiók létrehozására lesz használva, és csak a varázsló befejeztéig.
![Kapcsolódás](./media/how-to-connect-install-move-db/db5.png)
 
14. A **címtárak csatlakozására** szolgáló képernyőn a címtár-szinkronizáláshoz konfigurált meglévő AD-erdő mellett egy piros kereszt ikon látható. Helyszíni AD-erdőből származó módosítások szinkronizálásához AD DS-fiók szükséges. Az Azure AD Connect varázslója nem tudja lekérni az ADSync-adatbázisban tárolt AD DS-fiók hitelesítő adatait, mert azok titkosítva vannak, és a titkosítás kizárólag az előző Azure AD Connect-kiszolgálóval oldható fel. Kattintson a **hitelesítő adatok módosításának** lehetőségére az AD-erdőhöz tartozó AD DS-fiók megadásához.
![Címtárak](./media/how-to-connect-install-move-db/db6.png)
 
 
15. Az előugró párbeszédpanelen a következő lehetőségek közül választhat: (i) megad vállalati rendszergazdai hitelesítő adatokat, és engedi, hogy az Azure AD Connect hozza létre az AD DS-fiókot, vagy (ii) létrehozza az AD DS-fiókot, és megadja annak hitelesítő adatait az Azure AD Connectnek. Miután kiválasztotta az egyik lehetőséget és megadta a szükséges hitelesítő adatokat, kattintson az **OK** gombra az előugró párbeszédpanel bezáráshoz.
![Üdvözlőképernyő](./media/how-to-connect-install-move-db/db7.png)
 
 
16. A hitelesítő adatok megadása után a piros kereszt ikon egy zöld pipa ikonra változik. Kattintson a **tovább**.
![Üdvözlőképernyő](./media/how-to-connect-install-move-db/db8.png)
 
 
17. A **Ready to configure** (Konfigurálásra kész) képernyőn kattintson az **Install** (Telepítés) gombra.
![Üdvözlőképernyő](./media/how-to-connect-install-move-db/db9.png)
 
 
18. Miután a telepítés befejeződött, az Azure AD Connect-kiszolgáló automatikusan engedélyezve lesz az átmeneti módhoz. Javasoljuk, hogy az átmeneti mód letiltása előtt ellenőrizze a kiszolgáló konfigurációját és a függőben lévő exportálásokat, nehogy nem várt módosításokkal kelljen számolnia. 

## <a name="next-steps"></a>További lépések

- További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
- [Az Azure AD Connect telepítése meglévő ADSync-adatbázis használatával](how-to-connect-install-existing-database.md)
- [Az Azure AD Connect telepítése SQL-lel delegált rendszergazdai engedélyekkel](how-to-connect-install-sql-delegation.md)

