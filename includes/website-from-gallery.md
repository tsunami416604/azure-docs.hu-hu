Az Azure Piactér a Microsoft, külső vállalatok és nyílt forrású szoftver-kezdeményezések által fejlesztett népszerű webalkalmazások széles skáláját teszi elérhetővé. Az Azure Marketplace-ről létrehozott webalkalmazások használatához nincs szükség semmilyen szoftver telepítésére az [Azure Betekintő portálhoz](http://go.microsoft.com/fwlink/?LinkId=529715) való csatlakozáshoz használt böngészőn kívül. 

Ezen oktatóanyag segítségével elsajátíthatja a következőket:

* Új webalkalmazás létrehozása az Azure Marketplace segítségével.
* A webalkalmazás telepítése az Azure Betekintő portálon.

Létrehoz egy WordPress blogot, amely egy alapértelmezett sablont használ. Az alábbi ábrán a kész alkalmazás látható:

![WordPress blog][13]

> [!NOTE]
> Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, lépjen [Az App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra, ahol azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="create-a-web-app-in-the-portal"></a>Webalkalmazás létrehozása a portálon
1. Jelentkezzen be az Azure Betekintő portálra.
2. Nyissa meg az Azure Marketplace-t a **Marketplace** ikonjára kattintva:
   
    ![Marketplace ikon][marketplace]
   
    Vagy kattintson az irányítópult jobb felső részén található **Új** ikonra, és válassza a **Marketplace** lehetőséget a lista alján.
   
    ![Új létrehozása][5]
3. Kattintson a **Web + mobil** elemre. Keresse meg a **WordPress** alkalmazást, majd kattintson a **WordPress** ikonra.
   
    ![A listában szereplő WordPress][7]
4. A WordPress alkalmazás leírásának elolvasása után kattintson a **Létrehozás** gombra.
5. Kattintson a **Webalkalmazás** elemre, és adja meg a webalkalmazás konfigurálásához szükséges értékeket.
   
    ![az alkalmazás konfigurálása][8]
6. Kattintson az **Adatbázis** elemre, és adja meg a MySQL-adatbázis konfigurálásához szükséges értékeket. 
   
    ![adatbázis konfigurálása][database]
7. Adja meg egy új erőforráscsoport nevét.
   
    ![Erőforráscsoport beállítása][groupname]
8. Ha szükséges, kattintson az **ELŐFIZETÉS** elemre, és válassza ki a használni kívánt előfizetést. 
9. Miután végzett a webalkalmazás meghatározásával, kattintson a **Létrehozás** elemre, és várjon, amíg a rendszer létrehozza az új webalkalmazást.
   
   Miután létrejött az alkalmazás, látni fogja a webalkalmazást és az adatbázist tartalmazó erőforráscsoportot.
   
   ![csoport megjelenítése][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>A WordPress-webalkalmazás elindítása és kezelése
1. Kattintson az új webalkalmazásra az alkalmazás részleteinek megtekintéséhez.
   
    ![irányítópult indítása][10]
2. Az **Alapvető szolgáltatások** oldalon kattintson a **Tallózás** lehetőségre vagy az **URL** alatt található hivatkozásra a webalkalmazás kezdőlapjának megnyitásához.
   
    ![webhely URL-címe][browse]
3. Ha nem telepítette a WordPresst, adja meg a WordPresshez szükséges konfigurációs információkat, majd kattintson a **WordPress telepítése** lehetőségre a konfiguráció véglegesítéséhez és a webalkalmazás bejelentkezési oldalának megnyitásához.
4. Kattintson a **Bejelentkezés** lehetőségre, majd adja meg a hitelesítő adatait.  
5. Az új WordPress webalkalmazása az alábbi webalkalmazásra hasonlít.    
   
    ![az Ön WordPress-webhelye][13]

[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png


<!--HONumber=Jan17_HO3-->


