<properties
    pageTitle="在多次失敗後登入"
    description="指出在多次連續登入嘗試失敗後成功登入的使用者的報告。"
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="gchander"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2015"
    ms.author="saah;kenhoff"/>

# 在多次失敗後登入
此報告指出在多次連續登入嘗試失敗後成功登入的使用者。 可能的原因包括： <ul><li>使用者忘記其密碼</li><li>使用者是成功密碼猜測暴力的犧牲者強制攻擊</li></ul><p>結果這份報表會向您顯示連續失敗次數嘗試成功的登入之前以及與第一次成功登入相關聯的時間戳記。</p><p><b>報告設定</b>︰ 您可以設定最小數目的連續失敗登入嘗試才不會顯示在報表中。 當您變更此設定時，請務必注意這些變更將不會套用到目前顯示於現有報表中的任何現有失敗登入。 不過會套用到未來所有的登入。 只有經過授權的管理員才可以變更此報告。


![在多次失敗後登入](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)


