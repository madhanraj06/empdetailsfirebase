
# Ex.No:1 To create a employee details fields and to display the employee details using Firebase Database in Android Studio.


## AIM:

To create and display the employee details using Firebase Database in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as HelloWorld and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Display the employee details in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the DatabaseTable using the firebasedatabase”.
Developed by: MADHANRAJ.P
Registeration Number : 21222322005
*/
```
### AndroidManifest.xml
```java
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.PMDEXP01">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

### Emp class
```java
package com.example.pmdexp_01;

public class Emp {
    public String name, age, salary;
    
    public Emp() {} // Required for Firebase
    
    public Emp(String name, String age, String salary) {
        this.name = name;
        this.age = age;
        this.salary = salary;
    }
}

```
### Main Activity
```java
package com.example.pmdexp_01;

import android.os.Bundle;
import android.widget.*;
import androidx.appcompat.app.AppCompatActivity;
import com.google.firebase.database.*;
import java.util.*;
public class MainActivity extends AppCompatActivity {
    EditText etName, etAge, etSalary;
    Button btnAdd, btnUpdate, btnDelete;
    ListView listView;
    DatabaseReference dbRef;
    ArrayList<String> items = new ArrayList<>();
    ArrayList<String> keys = new ArrayList<>();
    ArrayAdapter<String> adapter;
    String selectedKey = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        etName = findViewById(R.id.etName);
        etAge = findViewById(R.id.etAge);
        etSalary = findViewById(R.id.etSalary);
        btnAdd = findViewById(R.id.btnAdd);
        btnUpdate = findViewById(R.id.btnUpdate);
        btnDelete = findViewById(R.id.btnDelete);
        listView = findViewById(R.id.listView);
        dbRef = FirebaseDatabase.getInstance().getReference("Employees");
        adapter = new ArrayAdapter<>(this,
                android.R.layout.simple_list_item_1, items);
        listView.setAdapter(adapter);
        dbRef.addValueEventListener(new ValueEventListener() {
            @Override
            public void onDataChange(DataSnapshot snapshot) {
                items.clear();
                keys.clear();
                for (DataSnapshot d : snapshot.getChildren()) {
                    Emp e = d.getValue(Emp.class);
                    if (e != null) {
                        items.add(e.name + " | " + e.age + " | " +
                                e.salary);
                        keys.add(d.getKey());
                    }
                }
                adapter.notifyDataSetChanged();
            }

            @Override
            public void onCancelled(DatabaseError error) {
            }
        });
        btnAdd.setOnClickListener(v -> {
            Emp e = new Emp(etName.getText().toString(),
                    etAge.getText().toString(), etSalary.getText().toString());
            dbRef.push().setValue(e);
            clearFields();
        });
        btnUpdate.setOnClickListener(v -> {
            if (selectedKey != null) {
                Emp e = new Emp(etName.getText().toString(),
                        etAge.getText().toString(), etSalary.getText().toString());
                dbRef.child(selectedKey).setValue(e);
                clearFields();
            } else {
                Toast.makeText(this, "Select an item first",
                        Toast.LENGTH_SHORT).show();
            }
        });
        btnDelete.setOnClickListener(v -> {
            if (selectedKey != null) {
                dbRef.child(selectedKey).removeValue();
                clearFields();
            } else {
                Toast.makeText(this, "Select an item first",
                        Toast.LENGTH_SHORT).show();
            }
        });
        listView.setOnItemClickListener((p, v, pos, id) -> {
            selectedKey = keys.get(pos);
            dbRef.child(selectedKey).get().addOnSuccessListener(d -> {
                Emp e = d.getValue(Emp.class);
                if (e != null) {
                    etName.setText(e.name);
                    etAge.setText(e.age);
                    etSalary.setText(e.salary);
                }
            });
        });
    }

    private void clearFields() {
        etName.setText("");
        etAge.setText("");
        etSalary.setText("");
        selectedKey = null;
    }
}
```

### Activity_XML
```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <EditText
        android:id="@+id/etName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Name" />

    <EditText
        android:id="@+id/etAge"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Age"
        android:inputType="number" />

    <EditText
        android:id="@+id/etSalary"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Salary"
        android:inputType="number" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btnAdd"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Add" />

        <Button
            android:id="@+id/btnUpdate"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Update" />

        <Button
            android:id="@+id/btnDelete"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Delete" />
    </LinearLayout>

    <ListView
        android:id="@+id/listView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</LinearLayout>
```

## OUTPUT
<img width="1919" height="1077" alt="image" src="https://github.com/user-attachments/assets/79cc94e1-3823-414c-b868-2fc652fc4a84" />


<img width="1919" height="961" alt="image" src="https://github.com/user-attachments/assets/0b4d3cd7-eaac-4bc9-8105-7094c219605c" />




## RESULT
Thus a Simple Android Application create a firebase database and to display the employee details using Firbase Real Time Database in Android Studio is developed and executed successfully.
