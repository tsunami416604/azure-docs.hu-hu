
## <a name="set-up-your-project"></a>A projekt beállítása

Szeretné ezt a mintát Android Studio-projekt letöltése helyette? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip), és ugorjon a [konfigurációs lépés](#create-an-application-express) konfigurálása a példakód azt végrehajtása előtt.

### <a name="create-a-new-project"></a>Új projekt létrehozása 
1.  Nyissa meg az Android Studiót, és válassza **fájl** > **új** > **új projekt**.
2.  Az alkalmazás neve, majd válassza ki **következő**.
3.  Válassza ki **API 21 vagy újabb (Android 5.0)**, majd válassza ki **következő**.
4.  Hagyja **üres tevékenység** , válassza ki a **következő**, majd válassza ki **Befejezés**.


### <a name="add-msal-to-your-project"></a>MSAL hozzáadása a projekthez
1.  Válassza ki az Android Studióban **Gradle parancsfájlok** > **build.gradle (modul: alkalmazás)**.
2.  A **függőségek**, az alábbi kódot:

    ```ruby  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>A csomaggal kapcsolatban

Az előzőekben látható kód a csomag telepíti a Microsoft hitelesítési könyvtár. MSAL kezeli az beszerzése, gyorsítótárazás, és az Azure Active Directory v2 végpont által védett API-k elérésére használt felhasználói jogkivonatokhoz frissítésekor.
<!--end-collapse-->

## <a name="create-the-application-ui"></a>Az alkalmazás felhasználói felület létrehozása

1. Ugrás a **res** > **elrendezés**, majd nyissa meg **activity_main.xml**. 
2. A tevékenység elrendezésének `android.support.constraint.ConstraintLayout` vagy más, a `LinearLayout`.
3. Adja hozzá a `android:orientation="vertical"` tulajdonságot a `LinearLayout` csomópont.
4. Illessze be az alábbi kódot a `LinearLayout` csomópont, az aktuális tartalom cseréje:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
