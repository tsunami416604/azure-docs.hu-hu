---
title: Tárfiókok konfigurálása az Azure Cost Managementben |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálja az Azure storage-fiókok és az AWS-tároló gyűjtők az Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: dab7100c97fab7e086352916ec222ec70a0d0400
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056691"
---
# <a name="configure-storage-accounts-for-cost-management"></a>Tárfiókok konfigurálása a Cost Management

<!--- intent: As a Cost Management user, I want to configure Cost Management to use my cloud service provider storage account to store my reports. -->

A Cost Management jelentéseinek mentheti a Cloudyn portál, az Azure storage vagy az AWS-tároló gyűjtők. A Cloudyn portálon a jelentések mentése az ingyenesen elérhető. Azonban a felhőszolgáltató tárolási mentése folyamatban van a jelentések nem kötelező, és további költséget áll. Ez a cikk segít az Azure storage-fiókok és az Amazon Web Services (AWS) storage gyűjtők a jelentések tárolására konfigurálása.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy Azure storage-fiókot vagy egy Amazon tárolási gyűjtőbe.

Ha nem rendelkezik Azure storage-fiókkal, létre kell hoznia egyet. Az Azure storage-fiók létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy tárfiókot](../storage/common/storage-quickstart-create-account.md).

Ha nem rendelkezik egy AWS simple storage service (S3) gyűjtőbe, akkor meg kell hoznia egyet. Az S3 gyűjtőt létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy gyűjtőt](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Az Azure storage-fiók konfigurálása

Konfigurálás, Azure storage a Cost Management használata rendkívül egyszerű. Gyűjtse össze a tárfiók részleteit, és másolja őket a Cloudyn portálon.

1. Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.
2. Kattintson a **minden szolgáltatás**válassza **tárfiókok**, görgessen lefelé a storage-fiókot, amelyet szeretne használni, és válassza ki azt a fiókot.
3. Az a storage-fiók oldalon alatt **beállítások**, kattintson a **Tárelérési kulcsok**.
4. Másolás a **tárfióknevet** és **kapcsolati karakterlánc** key1 alatt.  
![Az Azure storage hozzáférési kulcsok](./media/storage-accounts/azure-storage-access-keys.png)  
5. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com webhelyre, és jelentkezzen be.
6. Kattintson a fogaskerék szimbólumra, majd **jelentések tárolókezelési**.
7. Kattintson a **új hozzáadása +** , és győződjön meg arról, hogy a Microsoft Azure van kiválasztva. Illessze be az Azure storage-fiók neve az a **neve** területen. Illessze be a **kapcsolati karakterlánc** a megfelelő területen. Adja meg a tároló nevét, és kattintson a **mentése**.  
![A Cloudyn tárolási kapacitással, az Azure-hoz](./media/storage-accounts/azure-cloudyn-storage.png)

  Az új jelentés az Azure storage bejegyzés jelenik meg a storage-fiók listában.  
    ![Új jelentés az Azure storage a listában](./media/storage-accounts/azure-storage-entry.png)


Jelentések az Azure-tárolóba mentheti. Az jelentésekben, kattintson a **műveletek** majd **jelentés ütemezése**. Jelentés elnevezése majd saját URL-címet, vagy használja az automatikusan létrehozott URL-címet. Válassza ki **Storage mentése** , és válassza ki a tárfiókot. Adja meg, amely lekérdezi a jelentés fájlnév fűzött előtagot. Válassza ki a CSV vagy JSON-fájl formátuma, és mentse a jelentést.

## <a name="configure-an-aws-storage-bucket"></a>Az AWS tárolási gyűjtőbe konfigurálása

A Cloudyn meglévő AWS hitelesítő adatait használja: felhasználó vagy szerepkör, a jelentések mentése a gyűjtőhöz. A hozzáférési tesztelni, Cloudyn próbál menteni egy kisméretű szöveges fájl a gyűjtőhöz a fájlnévvel _jelölőnégyzet-gyűjtőben-permission.txt_.

Azt adja meg a Cloudyn-szerepkör vagy felhasználó PutObject engedéllyel rendelkező a gyűjtőhöz. Ezután használja egy meglévő gyűjtőbe, vagy hozzon létre egy új jelentés mentéséhez. Végül döntse el, a tárolási osztály kezelése, életciklus-szabályokat állíthat be, vagy távolítsa el a felesleges fájlokat.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Engedélyek hozzárendelése az AWS-felhasználó vagy szerepkör

Amikor létrehoz egy új szabályzatot, meg kell adnia egy S3 gyűjtőt mentéséhez szükséges pontos engedélyekkel.

1. Jelentkezzen be az AWS konzolon, és válassza ki **szolgáltatások**.
2. Válassza ki **IAM** a szolgáltatások listájából.
3. Válassza ki **házirendek** a konzolon, és kattintson a bal oldali **házirend létrehozása**.
4. Kattintson a **JSON** fülre.
5. A következő szabályzatot lehetővé teszi, hogy egy S3 gyűjtőt mentéséhez. Másolja és illessze be az alábbi házirend példát a **JSON** fülre. Cserélje le &lt;bucketname&gt; a gyűjtő nevére.

  ```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
}
```

6. Kattintson a **szabályzat áttekintése**.  
    ![Szabályzat áttekintése](./media/storage-accounts/aws-policy.png)  
7. A szabályzat áttekintése lapon írja be a házirend nevét. Ha például _CloudynSaveReport2S3_.
8. Kattintson a **hozzon létre házirendet**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>A szabályzat csatolása a Cloudyn-szerepkör vagy felhasználó fiókjában

Csatlakoztassa az új házirend, az AWS konzolon nyissa meg, és szerkessze a Cloudyn-szerepkör vagy a felhasználó.

1. Jelentkezzen be az AWS konzolon, és válassza ki **szolgáltatások**, majd **IAM** a szolgáltatások listájából.
2. Ezek közül bármelyikre **szerepkörök** vagy **felhasználók** a konzol bal oldalán.

**A szerepkörök:**

  1. Kattintson a Cloudyn-szerepkör nevét.
  2. Az a **engedélyek** lapra, majd **szabályzat csatolása**.
  3. Keresse meg a létrehozott szabályzatot, és válassza ki, majd kattintson **szabályzat csatolása**.
    ![AWS – szabályzat szerepkör csatolása](./media/storage-accounts/aws-attach-policy-role.png)

**A felhasználók számára:**

1. Válassza ki a Cloudyn felhasználói.
2. Az a **engedélyek** lapra, majd **engedélyek hozzáadása**.
3. Az a **engedély megadása a** szakaszban jelölje be **a meglévő szabályzatok közvetlen csatolása**.
4. Keresse meg a létrehozott szabályzatot, és válassza ki, majd kattintson **tovább: tekintse át**.
5. Kattintson a szerepkör neve lap Hozzáadás engedélyekkel, **engedélyek hozzáadása**.  
    ![AWS – felhasználó szabályzatának csatolása](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Választható lehetőség: Engedély beállítása a bucket szabályzat

Jelentéseket hozhat létre, a gyűjtő házirend segítségével S3 gyűjtő számára is beállíthatja. A klasszikus S3 nézetben:

1. Létrehozhat vagy kiválaszthat egy meglévő gyűjtőbe.
2. Válassza ki a **engedélyek** fülre, majd **házirend gyűjtőhöz**.
3. Másolja és illessze be a következő házirend-mintát. Cserélje le &lt;gyűjtőbe\_neve&gt; és &lt;Cloudyn\_elv&gt; az az információ, a gyűjtő. Cserélje le a szerepkör vagy a Cloudyn által használt felhasználói információ.

  ```
{
  "Id": "Policy1485775646248",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
  ]
}
```

4. A gyűjtő csoportházirend-szerkesztőben kattintson **mentése**.

### <a name="add-aws-report-storage-to-cloudyn"></a>A Cloudyn AWS jelentés tárhely hozzáadása

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com webhelyre, és jelentkezzen be.
2. Kattintson a fogaskerék szimbólumra, majd **jelentések tárolókezelési**.
3. Kattintson a **új hozzáadása +** , és győződjön meg arról, hogy az AWS van kiválasztva.
4. Válassza ki egy fiókot és a storage gyűjtőbe. Az AWS-tároló gyűjtőbe neve automatikusan kitöltve jelennek.  
    ![Az AWS gyűjtők jelentés tároló hozzáadása](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Kattintson a **mentése** majd **Ok**.

    Az új AWS jelentés tárolási bejegyzés jelenik meg a storage-fiókok listájának.  
    ![Új AWS jelentés tárolót a listában](./media/storage-accounts/aws-storage-entry.png)


Jelentések az Azure-tárolóba mentheti. Az jelentésekben, kattintson a **műveletek** majd **jelentés ütemezése**. Jelentés elnevezése majd saját URL-címet, vagy használja az automatikusan létrehozott URL-címet. Válassza ki **Storage mentése** , és válassza ki a tárfiókot. Adja meg, amely lekérdezi a jelentés fájlnév fűzött előtagot. Válassza ki a CSV vagy JSON-fájl formátuma, és mentse a jelentést.

## <a name="next-steps"></a>További lépések

- Felülvizsgálat [Understanding management költségjelentéseket](understanding-cost-reports.md) az alapszintű struktúrát és költségkezelési jelentések funkcióit ismerteti.
