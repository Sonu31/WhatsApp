# WhatsApp
![Screenshot_20210909-235640_WhatsApp 1](https://user-images.githubusercontent.com/81131231/132747558-a8e1b155-fe7d-4156-bb94-377abefca888.jpg)

package com.example.whatsapp;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Toast;

import com.example.whatsapp.Models.Users;
import com.example.whatsapp.databinding.ActivitySignUp2Binding;

import com.example.whatsapp.databinding.ActivitySigninBinding;
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.auth.AuthResult;
import com.google.firebase.auth.FirebaseAuth;
import com.google.firebase.database.FirebaseDatabase;

public class SignUpActivity extends AppCompatActivity {
    ActivitySignUp2Binding binding;
    private FirebaseAuth auth;
    FirebaseDatabase database;

    ProgressDialog progressDialog;
    @NonNull


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        binding= ActivitySignUp2Binding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());

        getSupportActionBar().hide();

        auth = FirebaseAuth.getInstance();
        database=FirebaseDatabase.getInstance();

        progressDialog= new ProgressDialog(SignUpActivity.this);
        progressDialog.setTitle("Creating Account");
        progressDialog.setMessage("we're creating your your account");

        binding.btnsingup.setOnClickListener(new View.OnClickListener(){
            @Override
            public  void onClick(View view){
                progressDialog.show();

                auth.createUserWithEmailAndPassword(
                        binding.etEmail.getText().toString(),binding.etpassword.getText().toString()).
                        addOnCompleteListener(new OnCompleteListener<AuthResult>() {
                    @Override

                    public void onComplete(@NonNull  Task<AuthResult> task) {
                        progressDialog.dismiss();
                        if(task.isSuccessful()){
                            Users user = new  Users(binding.UserName.getText().toString(),binding.etEmail.getText().toString(),
                                    binding.etpassword.getText().toString());

                            String id =task.getResult().getUser().getUid();
                            database.getReference().child("Users").child(id).setValue(user);


                            Toast.makeText(SignUpActivity.this,"User Created Successfully",Toast.LENGTH_SHORT).show();
                        }
                        else
                        {
                            Toast.makeText(SignUpActivity.this,task.getException().getMessage() , Toast.LENGTH_SHORT).show();
                        }

                    }
                });

            }

        });
        binding.alreadyaccount.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent intent = new Intent(SignUpActivity.this,SigninActivity.class);
                startActivity(intent);
            }
        });




    }
}
