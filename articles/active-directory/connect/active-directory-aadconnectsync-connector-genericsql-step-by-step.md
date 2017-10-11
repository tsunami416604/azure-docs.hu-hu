---
title: "Általános SQL összekötő lépés-lépésre |} Microsoft Docs"
description: "Ez a cikk van érdekében egy egyszerű HR rendszer részletes az általános SQL-összekötővel."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 28c1cc60-24fd-4d0d-a36d-b4aba6de86e7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 3fdc1b405b95180d031aa4ad45b406f7fc149d8f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="generic-sql-connector-step-by-step"></a>Általános SQL-összekötő – részletes útmutató
Ez a témakör részletesen ismerteti az. Egy egyszerű példa HR adatbázist hoz létre, és használja azt az egyes felhasználók és a csoporttagságuk importálásához.

## <a name="prepare-the-sample-database"></a>A minta-adatbázis előkészítése
A kiszolgálón futó SQL Server, az SQL-parancsfájl futtatása található [függelék](#appendix-a). Ezt a parancsfájlt a mintaadatbázis GSQLDEMO nevű hoz létre. Az adatbázis létrehozása a hálózatiobjektum-modellt a kép néz ki:  
![Hálózatiobjektum-modellje](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/objectmodel.png)

A felhasználó az adatbázishoz való kapcsolódáshoz használni kívánt is létrehozhat. Ebben a bemutatóban a felhasználó FABRIKAM\SQLUser nevezik, és a tartományban található.

## <a name="create-the-odbc-connection-file"></a>Az ODBC-kapcsolat fájl létrehozása
Az általános SQL-összekötő ODBC használja a távoli kiszolgálóhoz való csatlakozáshoz. Először kell hozzon létre egy fájlt az ODBC-kapcsolódási információt.

1. Indítsa el az ODBC segédprogramja a kiszolgálón:  
   ![ODBC](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc.png)
2. Válassza ki a lapon **fájl DSN**. Kattintson a **hozzáadása...** .  
   ![ODBC1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc1.png)
3. A out-of-box illesztőprogram works azokat, így parancsára **következő >**.  
   ![ODBC2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc2.png)
4. Nevezze el a fájlt, például a **GenericSQL**.  
   ![ODBC3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc3.png)
5. Kattintson a **Befejezés** gombra.  
   ![ODBC4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc4.png)
6. A kapcsolat beállítása idő. Az adatforrás adjon meg helyes leírást, és adja meg az SQL Server rendszert futtató kiszolgáló nevét.  
   ![ODBC5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc5.png)
7. Válassza ki, hogyan hitelesítheti az SQL. Ebben az esetben azt a Windows-hitelesítés használatára.  
   ![ODBC6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc6.png)
8. Adja meg a mintaadatbázis nevét **GSQLDEMO**.  
   ![ODBC7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc7.png)
9. Minden alapértelmezett ezen a képernyőn megtartása. Kattintson a **Befejezés** gombra.  
   ![ODBC8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc8.png)
10. Ellenőrizze, hogy minden a várt módon működik, kattintson a **tesztelése adatforrás**.  
    ![ODBC9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc9.png)
11. Győződjön meg arról, hogy a teszt sikeres.  
    ![ODBC10](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc10.png)
12. Az ODBC-konfigurációs fájl most kell fájl DSN látható.  
    ![ODBC11](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc11.png)

Most már tudunk a fájlt, igazolnia kell, és az összekötő létrehozásához.

## <a name="create-the-generic-sql-connector"></a>Az általános SQL-összekötő létrehozása
1. A Synchronization Service Manager felhasználói felületén válassza ki a **összekötők** és **létrehozása**. Válassza ki **általános SQL (Microsoft)** , és adjon neki könnyen megjegyezhető nevet.  
   ![Connector1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector1.png)
2. Az előző szakaszban létrehozott DSN fájl található, és töltse fel a kiszolgálón. Adja meg az adatbázishoz való kapcsolódáshoz.  
   ![Connector2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector2.png)
3. Ebben a forgatókönyvben azt van így könnyen nekünk, és tegyük fel például, hogy nincsenek-e két objektumtípusok **felhasználói** és **csoport**.
   ![Connector3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector3.png)
4. Az attribútumok kereséséhez azt szeretnénk, az összekötő azok észleléséhez a tábla részei megtekintésével. Mivel a **felhasználók** egy fenntartott szó SQL, igazolnia kell a szögletes zárójelbe [] biztosításához.  
   ![Connector4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector4.png)
5. Adja meg a horgonyattribútum és a megkülönböztető név attribútum idő. A **felhasználók**, a két attribútum felhasználónév és a EmployeeID használjuk. A **csoport**, GroupName használjuk (nem fog reális valósághű, de ez a forgatókönyv működik).
   ![Connector5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector5.png)
6. Nem minden attribútumtípust észlelhető az SQL-adatbázisban. A hivatkozási attribútum típusa különösen nem. A csoport objektumtípus módosítsa a OwnerID és a MemberID kell hivatkoznia kell.  
   ![Connector6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector6.png)
7. A jelenleg kijelölt összes hivatkozás attribútumok az előző lépésben beállítást az objektumtípus ezeket az értékeket attribútumokat mutató hivatkozás. Ebben az esetben a felhasználó objektum típusa.  
   ![Connector7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector7.png)
8. A globális paraméterek lapon válassza az **vízjel** , a különbözeti stratégia. A dátum és idő formátumban is beírhat **éééé-hh-nn óó: pp:**.
   ![Connector8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector8.png)
9. Az a **konfigurálása partíciók és hierarchiák** lapon, válassza ki mindkét típusú objektumokat.
   ![Connector9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector9.png)
10. Az a **típusú objektumokat válasszon** és **attribútumok kiválasztása**, jelölje be a típusú objektumokat és az összes attribútum. Az a **horgonyok konfigurálása** kattintson **Befejezés**.

## <a name="create-run-profiles"></a>Futtatási profilok létrehozása
1. A Synchronization Service Manager felhasználói felületén válassza ki a **összekötők**, és **Configure Run Profiles**. Kattintson a **új profil**. Először **teljes importálás**.  
   ![Runprofile1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile1.png)
2. Válassza ki a **teljes importálás (csak szakaszban)**.  
   ![Runprofile2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile2.png)
3. Válassza ki a partíciót **objektum = felhasználó**.  
   ![Runprofile3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile3.png)
4. Válassza ki **tábla** és típus **[felhasználók]**. Görgessen le a többértékű objektum típushoz című rész, és írja be az adatokat, ahogy az alábbi képen. Válassza ki **Befejezés** menteni a lépést.  
   ![Runprofile4a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4a.png)  
   ![Runprofile4b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4b.png)  
5. Válassza ki **új lépés**. Most, jelölje be **objektum = csoport**. Az utolsó oldalon ahogy az alábbi képen konfigurációt használja. Kattintson a **Befejezés** gombra.  
   ![Runprofile5a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5a.png)  
   ![Runprofile5b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5b.png)  
6. Nem kötelező: Ha kívánja, konfigurálhatja a további futtatási profilokat. Ez a forgatókönyv csak a teljes importálás használjuk.
7. Kattintson a **OK** futtatási profilok módosításának befejezéséhez.

## <a name="add-some-test-data-and-test-the-import"></a>Adja hozzá az egyes adatok tesztelésére, és tesztelje az importálás
Töltse ki a mintaadatbázis néhány tesztadatot. Amikor elkészült, válassza ki a **futtatása** és **teljes importálás**.

Ez a felhasználó két telefonszámot és egy csoport néhány tagjához.  
![cs1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs1.png)  
![CS2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs2.png)  

## <a name="appendix-a"></a>A függelék
**SQL-parancsfájl a mintaadatbázis létrehozásához**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
