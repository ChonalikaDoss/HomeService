# HomeService
MainActivity.java
package com.ashwin.myservices;
import android.appcompat.app.AppCompatActivity;
import android.content.Intent;
import android.os.Bundle;
public class MainActivity extends AppCompatActivity {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Intent intent = new Intent(this, Login.class);
    finish();
    startActivity(intent);
  }
}
Login.java
package com.ashwin.myservices;
import androidx.annotation.NonNull;
import android.appcompat.app.AppCompatActivity;

import android.content.Context;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.EditText;
import android.widget.ProgressBar;
import android.widget.Toast;

import com.google.firebase.database.DataSnapshot;
import com.google.firebase.database.DatabaseError;
import com.google.firebase.database.DatabaseReference;
import com.google.firebase.database.FirebaseDatabase;
import com.google.firebase.database.ValueEventListener;

public class Login extends AppCompatActivity {
  Context context;
  FirebaseDatabase database;
  DatabaseReference ref;
  EditText phno, password;
  ProgressBar progressBar;
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_login);
    context = this;
    phno = findViewById(R.id.phno);
    password = findViewById(R.id.password);
    progressBar = findViewById(R.id.progressBar);
    database = FirebaseDatabase.getInstance();
  }

  public void loginButton(View view) {
    progressBar.setVisibility(View.VISIBLE);
    view.setVisibility(View.GONE);
    String phnoString = phno.getText().toString();
    if(phnoString.length() > 0 && phnoString.length() <= 10) {
      ref = database.getReference("users/"+phnoString);
      ref.addValueEventListener(new ValueEventListener() {
        @Override
        public void onDataChange(@NonNull DataSnapshot snapshot) {
          progressBar.setVisibility(View.GONE);
          view.setVisibility(View.VISIBLE);
          if (snapshot.getValue() == null) {
            Toast.makeText(context, "No Account Found", Toast.LENGTH_SHORT).show();
          } else {
            String pass = (String) snapshot.child("password").getValue();
            assert pass != null;
            if(pass.equals(password.getText().toString())) {
              Intent intent = new Intent(context, welcome.class);
              finish();
              startActivity(intent);
            } else {
              Toast.makeText(context, "Incorrect Password", Toast.LENGTH_SHORT).show();
            }
          }
        }
    @Override
        public void onCancelled(@NonNull DatabaseError error) {
          progressBar.setVisibility(View.GONE);
          view.setVisibility(View.VISIBLE);
          Toast.makeText(context, "Error Code: 13", Toast.LENGTH_SHORT).show();
        }
      });
    } else {
      progressBar.setVisibility(View.GONE);
      view.setVisibility(View.VISIBLE);
      Toast.makeText(context,"Enter An Valid Phone Number", Toast.LENGTH_SHORT).show();
    }
  }
}
Service.java
package com.ashwin.myservices;

import androidx.annotation.NonNull;
import android.appcompat.app.AppCompatActivity;

import android.content.Context;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.ProgressBar;
import android.widget.Toast;

import com.google.firebase.database.DataSnapshot;
import com.google.firebase.database.DatabaseError;
import com.google.firebase.database.DatabaseReference;
import com.google.firebase.database.FirebaseDatabase;
import com.google.firebase.database.ValueEventListener;

public class Services extends AppCompatActivity {

  LinearLayout serviceList;
  ProgressBar progressBar2;
  FirebaseDatabase database;
  Context context;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_services);
   context = this;
    progressBar2 = findViewById(R.id.progressBar2);
    serviceList = findViewById(R.id.serviceList);
   database = FirebaseDatabase.getInstance();

    DatabaseReference ref = database.getReference("Services/");
    ref.addValueEventListener(new ValueEventListener() {
      @Override
      public void onDataChange(@NonNull DataSnapshot snapshot) {
        progressBar2.setVisibility(View.GONE);
        for(DataSnapshot data : snapshot.getChildren()) {
          LayoutInflater inflater = (LayoutInflater) getSystemService(Context.LAYOUT_INFLATER_SERVICE);
          View tile = inflater.inflate(R.layout.serviceslisttile, null);
          Button tileButton = tile.findViewById(R.id.TileButton);
          tileButton.setText(String.valueOf(data.getValue()));
          tileButton.setOnClickListener(view -> {
            System.out.println("bang");
          });
          serviceList.addView(tile);
        }
      }
     @Override
      public void onCancelled(@NonNull DatabaseError error) {
        Toast.makeText(context, "Error Code: 13", Toast.LENGTH_SHORT).show();
      }
    });
  }
}
Welcome.java
package com.ashwin.myservices;
import android.appcompat.app.AppCompatActivity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;

public class welcome extends AppCompatActivity {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_welcome);
  }
  public void onServicesClick(View view) {
    Intent intent = new Intent(this, Services.class);
    startActivity(intent);
  }
}
