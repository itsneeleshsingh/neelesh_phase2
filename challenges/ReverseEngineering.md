# 4. VeridisQuo
The challenge wants us to analyze an Android application package (APK) file to extract hidden information. We are required to decompile the APK to inspect its structure and code and find flags embedded within.

## Solution:
1. I was provided with an APK file named `VeridisQuo.apk` to analyze.
2. To decompile the APK I used an online [APK decompiler](http://www.javadecompilers.com/).
    ![image1](images/VeridisQuo1.png)
3. After decompilation, I downloaded the resulting ZIP file and extracted its contents on my PC in a folder.
    ![image1](images/VeridisQuo2.png)
4. After the extraction, it had two folders: `resources` and `sources`. Then I read some apk file structures in java in a blog article.
5. After some analysing and applying strings on some files, I found some strings on the `classes2.dex` file found in the `resources` directory using the command:
    ```bash
    ┌──(neels㉿neel)-[~/CustomQuesCryptonite/ReverseEngineering/VeridisQuo/resources]
    └─$ strings classes2.dex | grep flag                      
            flagPart1
    flagPart10
    flagPart11
    flagPart12
    flagPart13
    flagPart14
    flagPart15
    flagPart16
    flagPart17
    flagPart18
    flagPart19
            flagPart2
    flagPart20
    flagPart21
    flagPart22
    flagPart23
    flagPart24
    flagPart25
    flagPart26
    flagPart27
    flagPart28
            flagPart3
            flagPart4
            flagPart5
            flagPart6
            flagPart7
            flagPart8
            flagPart9
    ```
6. This command returned several parts fo flag named `flagPart1`, `flagPart2`, up to `flagPart28`, So I understand that I have to find some parts of the flag.
7. Next I explored the `sources` folder and discovered a unique directory named `byuctf` which looked usefull to me and it contained three files: `MainActivity.java`, `R.java`, and `Utilities.java`.
8. While exploring, In `Utilities.java` I noticed the following code that referenced the flag parts:
    ```java
    ┌──(neels㉿neel)-[~/…/VeridisQuo/sources/byuctf/downwiththefrench]
    └─$ cat Utilities.java         
    package byuctf.downwiththefrench;

    import android.app.Activity;
    import android.widget.TextView;

    public class Utilities {
        private Activity activity;

        public Utilities(Activity activity2) {
            this.activity = activity2;
        }

        public void cleanUp() {
            ((TextView) this.activity.findViewById(R.id.flagPart1)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart2)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart3)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart4)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart5)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart6)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart7)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart8)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart9)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart10)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart11)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart12)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart13)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart14)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart15)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart16)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart17)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart18)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart19)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart20)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart21)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart22)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart23)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart24)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart25)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart26)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart27)).setText("");
            ((TextView) this.activity.findViewById(R.id.flagPart28)).setText("");
        }
    }
    ```

9. This code implies that flag parts are linked to specific TextViews using their IDs. From some previous little knowledge of android development, I know that a page(Java page) connects to a xml file, so I began to search for xml files.
10. After here and there, while checking the `res/layout/` directory, I found `activity_main.xml` which contained the definitions for the flag parts as TextViews each pointing to a character of the flag.
    ![image1](images/VeridisQuo3.png)
    ```xml
    <androidx.constraintlayout.widget.ConstraintLayout android:layout_width="match_parent" android:layout_height="match_parent">
    <TextView android:id="@+id/homeText" android:layout_width="wrap_content" android:layout_height="wrap_content" android:text="b" app:layout_constraintBottom_toBottomOf="0" app:layout_constraintEnd_toEndOf="0" app:layout_constraintHorizontal_bias="0.066" app:layout_constraintStart_toStartOf="0" app:layout_constraintTop_toTopOf="0" app:layout_constraintVertical_bias="0.022"/>
    <TextView android:id="@+id/flagPart1" android:layout_width="wrap_content" android:layout_height="wrap_content" android:layout_marginBottom="420dp" android:text="}" android:layout_marginEnd="216dp" app:layout_constraintBottom_toBottomOf="0" app:layout_constraintEnd_toEndOf="0"/>
    <TextView android:id="@+id/flagPart2" android:layout_width="wrap_content" android:layout_height="wrap_content" android:layout_marginBottom="616dp" android:text="t" android:layout_marginEnd="340dp" app:layout_constraintBottom_toBottomOf="0" app:layout_constraintEnd_toEndOf="0"/>
    <TextView android:id="@+id/flagPart3" android:layout_width="wrap_content" android:layout_height="wrap_content" android:layout_marginBottom="556dp" android:text="a" android:layout_marginEnd="332dp" app:layout_constraintBottom_toBottomOf="0" app:layout_constraintEnd_toEndOf="0"/>
    <TextView android:id="@+id/flagPart4" android:layout_width="wrap_content" android:layout_height="wrap_content" android:layout_marginBottom="676dp" android:text="y" android:layout_marginEnd="368dp" app:layout_constraintBottom_toBottomOf="0" app:layout_constraintEnd_toEndOf="0"/>
    <TextView android:id="@+id/flagPart5" android:layout_width="wrap_content" android:layout_height="wrap_content" android:layout_marginBottom="500dp" android:text="c" android:layout_marginEnd="252dp" app:layout_constraintBottom_toBottomOf="0" app:layout_constraintEnd_toEndOf="0"/>
    (similarly all)
    ```

11. After extracting the characters from each of these TextViews, I decoded the flag(assuming `BYUCTF` as the header part of flag).

## Flag:
```
BYUCTF{tayccdrni0dkupocfe_e_fi_4e}
```

## Concepts learnt:
- Understanding the Android application file structure.
- Utilizing APK decompilers for reverse engineering.
- Identifying and extracting Java classes and XML layout resources.
- Using strings commands to search for relevant data in compiled files.

## Notes:
- I initially went off track by randomly exploring various folders without a clear strategy which slowed down my progress.
- It took some time to realize the connection between the Java files and the XML layout, as I initially overlooked searching for XML resources.

## Resources:
- Online APK decompiler: [javadecompilers.com](http://www.javadecompilers.com/)
- Overview of the Android file structure: [GeeksforGeeks](https://www.geeksforgeeks.org/android/android-android-apps-file-structure/)