---
title: Oktatóanyag – Azure Analysis Services rendszergazdai és felhasználói szerepkörök konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure Analysis Services-szerepkörök.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: a1e6e04c4019086d56cc0eb25176a507e514c477
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000642"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>Oktatóanyag: Kiszolgáló-rendszergazdai és felhasználói szerepkörök konfigurálása

 Ebben az oktatóanyagban az SQL Server Management Studio (SSMS) használatával fog csatlakozni Azure-beli kiszolgálójához, hogy konfigurálja a kiszolgáló rendszergazdája és a modelladatbázis szerepköreit. Megismerheti a [Táblázatos modell parancsnyelvét (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200) is. A TMSL egy JSON-alapú parancsnyelv az 1200-as és magasabb kompatibilitási szintű táblázatos modellekhez. Számos táblázatos modellbeli feladat automatizálására használható. A TMSL-t gyakran a PowerShellel együtt használják, ebben az oktatóanyagban azonban az XMLA lekérdezésszerkesztőt fogja használni az SSMS-ben. Az oktatóanyag során a következő feladatokat fogja elvégezni: 
  
> [!div class="checklist"]
> * Kiszolgálónév lekérdezése a portálról
> * Csatlakozás a kiszolgálóhoz az SSMS használatával
> * Felhasználó vagy csoport hozzáadása a kiszolgáló-rendszergazdai szerepkörhöz 
> * Felhasználó vagy csoport hozzáadása a modelladatbázis-rendszergazdai szerepkörhöz
> * Új modelladatbázis-szerepkör hozzáadása és felhasználó vagy csoport felvétele

Az Azure Analysis Services felhasználói biztonságáról a [Hitelesítés és felhasználói engedélyek](../analysis-services-manage-users.md) című cikkből tájékozódhat bővebben. 

## <a name="prerequisites"></a>Előfeltételek

- Létezik egy Azure Active Directory az előfizetésében.
- Létrehozott egy [Azure Analysis Services-kiszolgálót](../analysis-services-create-server.md) az előfizetésében.
- [Kiszolgáló-rendszergazdai](../analysis-services-server-admins.md) engedélyekkel rendelkezik.
- [Hozzáadta az adventureworks mintamodellt](../analysis-services-create-sample-model.md) a kiszolgálójához.
- [Telepítette az SQL Server Management Studio (SSMS) legújabb verzióját](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a [portálra](https://portal.azure.com/).

## <a name="get-server-name"></a>Kiszolgálónév lekérése
Ahhoz, hogy az SSMS-ből csatlakozni tudjon a kiszolgálójához, szüksége lesz a kiszolgáló nevére. A kiszolgálónevet a portálról kérheti le.

Másolja a kiszolgáló nevét az **Azure Portal** > kiszolgáló > **Áttekintés** > **Kiszolgálónév** részéből.
   
   ![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>Csatlakozás az SSMS-ben

A további feladatokban az SSMS használatával csatlakozhat a kiszolgálóhoz és kezelheti azt.

1. Az SSMS > **Object Explorerben** kattintson a **Csatlakozás** > **Analysis Services** lehetőségre.

    ![Kapcsolódás](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. A **Csatlakozás kiszolgálóhoz** párbeszédablakban a **Kiszolgálónév** mezőbe illessze be a portálról kimásolt nevet. A **Hitelesítés** mezőben válassza az **Active Directory univerzális, MFA-támogatással** lehetőséget, majd adja meg felhasználói fiókját, és kattintson a **Csatlakozás** gombra.
   
    ![Csatlakozás az SSMS-ben](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > Javasolt az Active Directory univerzális hitelesítést választani MFA-támogatással. Ez a hitelesítés-típus támogatja a [nem interaktív és többtényezős hitelesítést](../../sql-database/sql-database-ssms-mfa-authentication.md). 

3. Az **Object Explorerben** bontsa ki a kiszolgálóobjektumokat. A jobb gombbal kattintva megjelenítheti a kiszolgáló tulajdonságait.
   
    ![Csatlakozás az SSMS-ben](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>Felhasználói fiók hozzáadása a kiszolgáló-rendszergazdai szerepkörhöz

Ebben a feladatban Azure AD-jából fog felhasználói vagy csoportfiókot hozzáadni a kiszolgáló-rendszergazdai szerepkörhöz. Biztonsági csoport hozzáadása esetében a `MailEnabled` tulajdonsághoz a `True` értéket kell beállítani.

1. Az **Object Explorerben** kattintson a jobb gombbal a kiszolgáló nevére, majd a **Tulajdonságok** lehetőségre. 
2. Az **Analysis Server-tulajdonságok** ablakban kattintson a **Biztonság** > **Hozzáadás** lehetőségre.
3. A **Felhasználó vagy csoport kijelölése** ablakban adjon meg egy felhasználói vagy csoportfiókot az Azure AD-ból, majd kattintson a **Hozzáadás** lehetőségre. 
   
     ![Kiszolgáló-rendszergazda hozzáadása](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. Az **Analysis Server-tulajdonságok** ablak bezárásához kattintson az **OK** gombra.

    > [!TIP]
    > Kiszolgáló-rendszergazdákat az **Analysis Services Admins** használatával is hozzáadhat a portálon. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>Felhasználó hozzáadása a modelladatbázis-rendszergazdai szerepkörhöz

Ebben a feladatban felhasználói vagy csoportfiókot fog hozzáadni a modellben már meglévő Internet Sales Administrator szerepkörhöz. Ez a szerepkör teljes (rendszergazdai) hozzáféréssel rendelkezik az adventureworks minta-modelladatbázishoz. A feladat a [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) TMSL-parancsot használja az Ön számára létrehozott szkriptben.

1. Az **Object Explorerben** bontsa ki az **Adatbázisok** > **adventureworks** > **Szerepkörök** elemet. 
2. Kattintson a jobb gombbal az **Internet Sales Administrator** elemre, majd kattintson a **Szerepkör szkriptelése:** > **Létrehozás vagy áthelyezés ide:** > **Új lekérdezésszerkesztő ablak**.

    ![Új lekérdezésszerkesztő ablak](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. Az **XMLAQuery** szkriptben írja át a **"memberName":** értékét egy Azure AD-beli felhasználói vagy csoportfiókra. Itt alapértelmezés szerint az a fiók szerepel, amellyel bejelentkezett, a saját fiókját azonban nem szükséges hozzáadnia, hiszen már kiszolgáló-rendszergazda.

    ![TMLS-szkript az XMLA-lekérdezésben](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. A szkript végrehajtásához nyomja le az **F5** billentyűt.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>Új modelladatbázis-szerepkör hozzáadása és felhasználó vagy csoport felvétele

Ebben a feladatban a [Create](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/create-command-tmsl?view=sql-analysis-services-2017) parancsot fogja használni egy TMSL-szkriptben egy új Internet Sales Global szerepkör létrehozására, *olvasási* engedélyt ad a szerepkörnek, és hozzáad egy felhasználói vagy csoportfiókot az Azure AD-ból.

1. Az **Object Explorerben** kattintson a jobb gombbal az **adventureworks** adatbázisra, majd kattintson az **Új lekérdezés** > **XMLA** elemre. 
2. Másolja ki és illessze be az alábbi TMSL-szkriptet a lekérdezésszerkesztőbe:

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. A `"memberName": "globalsales@adventureworks.com"` objektum értékét írja át egy Azure AD-beli felhasználói vagy csoportfiókra.
4. A szkript végrehajtásához nyomja le az **F5** billentyűt.

## <a name="verify-your-changes"></a>Módosítások ellenőrzése

1. Az **Object Explorerben** kattintson a kiszolgálója nevére, majd kattintson a **Frissítés** lehetőségre, vagy nyomja le az **F5** billentyűt.
2. Bontsa ki az **Adatbázisok** > **adventureworks** > **Szerepkörök** elemet. Ellenőrizze, hogy az előző feladatban hozzáadott felhasználói fiók és az új szerepkör-módosítások megjelennek-e.   

    ![Ellenőrzés az Object Explorerben](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha többé nincs rájuk szüksége, törölje a felhasználói és csoportfiókokat és szerepköröket. A felhasználói fiókok eltávolításához használja a **Szerepkör tulajdonságai** > **Tagság** lehetőséget. Kattintson a jobb gombbal egy szerepkörre, majd kattintson a **Törlés** lehetőségre.


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanult csatlakozni Azure AS-kiszolgálójához, megismerkedett az adventureworks minta-modelladatbázissal és annak tulajdonságaival az SSMS-ben. Elsajátította az SSMS és a TMSL-szkriptek használatát felhasználók vagy csoportok meglévő vagy új szerepkörökhöz adására. Most, hogy konfigurálta a kiszolgáló és a minta-modelladatbázis felhasználói engedélyeit, Önnel együtt mások is csatlakozhatnak ahhoz olyan ügyfélalkalmazások használatával, mint a Power BI. További tudnivalók a következő oktatóanyagban. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Csatlakozás a Power BI Desktoppal](analysis-services-tutorial-pbid.md)

