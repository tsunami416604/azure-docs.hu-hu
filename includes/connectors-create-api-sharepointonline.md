

Használata esetén csatlakozhassanak az **SharePoint Online**, meg kell adnia az identitás (felhasználónév és jelszó, intelligens kártyához tartozó hitelesítő adatok, stb.) a SharePoint online. Amennyiben Ön már hitelesítve, a SharePoint Online-összekötő használatához a logic App lépne. 

A Logic Apps alkalmazást designer, a következő lépések segítségével létrehozásához jelentkezzen be a SharePoint a **kapcsolat** használható a Logic Apps alkalmazást:

1. A keresőmezőbe írja be a SharePoint, és várja meg, a keresés vissza eseményindítók és a SharePoint online-hoz kapcsolódó műveletek:   
   ![A SharePoint konfigurálása][1]  
2. Válassza ki a **SharePoint online-hoz - fájl jön létre** eseményindító  
3. Válassza ki **jelentkezzen be a SharePoint Online**:   
   ![A SharePoint konfigurálása][2]    
4. Jelentkezzen be a SharePoint való hitelesítéshez szükséges a SharePoint hitelesítő adatok megadása   
   ![A SharePoint konfigurálása][3]     
5. A hitelesítés befejezése után a lesz átirányítva, a logikai alkalmazáshoz. Ennyi az egész, a kapcsolat létrejött. Figyelje meg, az üzenet azt jelzi, hogy most már csatlakozott SharePoint alján.  
   ![A SharePoint konfigurálása][4]  
6. Más eseményindítók és műveletek végre kell hajtania a Logic Apps alkalmazást, majd adja hozzá.   

[1]: ./media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ./media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ./media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ./media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ./media/connectors-create-api-sharepointonline/connectionconfig5.png
