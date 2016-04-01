<properties 
    pageTitle="Data Factory - 命名規則 | Microsoft Azure" 
    description="描述 Data Factory 實體的命名規則。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/15/2015" 
    ms.author="spelluru"/>

# Azure Data Factory - 命名規則 
下表提供 Data Factory 購件命名規則。



名稱 | 名稱唯一性 | 驗證檢查
:--- | :-------------- | :----------------
Data Factory | 在 Microsoft Azure 中是唯一的。 名稱不區分大小寫，例如 MyDF 和 mydf 是指相同的 Data Factory。 |<ul><li>每個資料處理站會繫結至一個 Azure 訂用帳戶。</li><li>物件名稱必須以字母或數字開頭，而且可以包含字母、 數字和虛線 （-） 字元。</li><li>每個虛線 （-） 字元必須立即前面和後面接著字母或數字。容器名稱中不允許連續虛線。</li><li>名稱可以是 3-63 個字元。</li></ul>
連結的服務/資料表/管線 | 在 Data Factory 中是唯一的。 名稱不區分大小寫。 | <ul><li>資料表名稱的字元數目上限 ︰ 260。</li><li>物件名稱必須以字母、 數字或底線 (_) 開頭。</li><li>下列字元不允許: 」。 」，"+"，"？"，"/"，"< 」，」 >"，"*"，"%"，"&"":"，"\\ 」</li></ul>
資源群組 | 在 Microsoft Azure 中是唯一的。 名稱不區分大小寫。 | <ul><li>最大字元數 ︰ 1000年。</li><li>名稱只能包含字母、 數字和下列字元:"-"，"_"，""and"。 」。</li></ul>

