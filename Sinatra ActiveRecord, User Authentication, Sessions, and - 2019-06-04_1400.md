# Sinatra ActiveRecord, User Authentication, Sessions, and RESTful Routes
> We will be going over concepts later on in the Sinatra curriculum, like ActiveRecord, User Authentication, Sessions, and RESTful Routes

## Table of Contents
 1. Sinatra RESTful Routes
 2. ActiveRecord in Sinatra
 3. Securing Passwords
 4. User Authentication and Sessions
 5. Helper methods (optional)


## ActiveRecord in Sinatra
- You can perform the CRUD actions using active record
- Make sure the students understand which controller actions render which views, and which views have forms that send requests to which controller actions:
![](../BearImages/EB77DDB6-0C55-4CE4-AAAE-EB095B906B9F-1817-0000019AA7BB673F/4o3Qtrv.png)
Look at the crud actions (and methods) on this page: [Active Record Basics — Ruby on Rails Guides](https://guides.rubyonrails.org/active_record_basics.html)
[Sinatra Activerecord Reading - Learn.co](https://learn.co/tracks/full-stack-web-development-v7/sinatra/activerecord/activerecord-in-sinatra)

## Sinatra RESTful Routes
- RESTful routes provides a design pattern that allows for easy data manipulation. It’s nicer for users and nicer for developers to have everything consistent.
- Many of the CRUD actions are different actions that occur on the same resource.
![](../BearImages/7A07C50A-E3A2-4A21-B671-40872856946A-1817-0000019AA7CE90D2/2702DDC1-A042-4952-AFFA-09B25AFE28DD.png)
![](../BearImages/8D079390-09CF-4CEF-A885-9802D17CD672-1817-0000019AA7E3354E/D54A835E-B832-4DCD-80C4-003FD70F1929.png)
[Rails Intro To Rest - Learn.co](https://learn.co/tracks/full-stack-web-development-v7/rails/intro-to-rest/intro-to-rest)

## Securing Passwords in Sinatra
- `password_digest ` in user table `t.string :password_digest`
- add the `has_secure_password`  macro to the user model
- Password Encryption with BCrypt
	- BCrypt will store a salted, hashed version of our users’ passwords in our database in a column called `password_digest`.
	- Encryption vs Hashing vs Salting
		- Encryption is a two-way function where information is scrambled in such a way that it can be unscrambled later. 
		- Hashing is a one-way function where data is mapped to a fixed-length value. Hashing is primarily used for authentication. 
		- Salting is an additional step during hashing, typically seen in association to hashed passwords, that adds an additional value to the end of the password that changes the hash value produced.
		- Read more at: https://www.thesslstore.com/blog/difference-encryption-hashing-salting/
- We can now use the `authenticate` method in our user controller

## User Authentication in Sinatra/Sessions
### Setting up Sessions in Sinatra
- Add code to your application controller:
```
configure do
  enable :sessions
  set :session_secret, "secret"
end
```
- More about the session secret here: [Sinatra: README](http://sinatrarb.com/intro.html)
- Now we can use sessions like this: session[user_id]

### Sign Up
1. User fills out form sends post to controller
2. We save a new user `User.create(name: params[:name], email: params[:email], password: params[:password])`
3. Sign the user in by setting the user’s id in the session hash

### Log In
- “logging” a user in is essentially storing the user’s ID in the `session` hash
- Login Flow:
	1. User visits login page, fills out form and hits submit to send a post request to the controller
	2. The controller route finds the user and stores the user’s ID in the session hash
	3. Now we can always grab the current user from the session hash

### Log Out
Clear the session hash `session.clear`







## Helper Methods
- `logged_in`
- `current_user`









## Other topics in this section (should we cover these?)
	- ActiveRecord Associations: Join Tables
	- Sinatra Multiple Controllers
	- Sinatra Complex Forms Associations
	




# On Associations (conversation with Nancy):
Ok, so the only thing I can think of is that we didn’t save an association. I think that might have been the problem. I used the appointment, patient, physician example but it should be the same for yours.

So, this is what I ran:
```
a = Appointment.create #Creates and saves a new appointment
This will give us a new appointment instance with no associations: #<Appointment id: 5, physician_id: nil, patient_id: nil, appointment_date: nil, created_at: "2019-06-11 00:39:09", updated_at: "2019-06-11 00:39:09">
Now we create a patient from that appointment:
a.create_patient(name: "Amelie")
This will give us a new patient: #<Patient id: 2, name: "Amelie", created_at: "2019-06-11 00:39:26", updated_at: "2019-06-11 00:39:26">
And if I look at my appointment, it shows the relationship to the patient: #<Appointment id: 5, physician_id: nil, patient_id: 2, appointment_date: nil, created_at: "2019-06-11 00:39:09", updated_at: "2019-06-11 00:39:09">
BUT, and this is what I think created our problems, when I look at the newly created patient's appointments with
Patient.last.appointments
The collection is empty BECAUSE we didn't save our association
Once we run
a.save
Patient.last.appointments
will return our correct association:
#<ActiveRecord::Associations::CollectionProxy [#<Appointment id: 5, physician_id: nil, patient_id: 2, appointment_date: nil, created_at: "2019-06-11 00:39:09", updated_at: "2019-06-11 00:39:51">]>
```

I think it may be something similar to what they mention in this lesson: https://learn.co/tracks/full-stack-web-development-v7/sinatra/activerecord/sinatra-complex-forms-associations

There is a part that says: `NOTE: When using the shovel operator, ActiveRecord instantly fires update SQL without waiting for the save or update call on the parent object, unless the parent object is a new record.`

(somehow they’re not actually saving it in the example which is weird) but I think that’s the same thing happening

https://stackoverflow.com/questions/18703036/when-will-activerecord-save-associations
I think that’s exactly what we’re talking about ^

There are also these `When are Objects Saved?` sections on the ruby page: https://guides.rubyonrails.org/association_basics.html
that’s right, so you have to make sure in that case to not only save the new object but also the association
with create it automatically saves the new object but not the association on the parent
oh, also I just saw this in my terminal. this is what happened when I saved the appointment (and therefore the association): `UPDATE "appointments" SET "patient_id" = ?, "updated_at" = ? WHERE "appointments"."id" = ?  [["patient_id", 2], ["updated_at", "2019-06-11 00:39:51.416891"], ["id", 5]]`

<!-- #blog #study-groups #sinatra# #ruby #needs-work -->

<!-- {BearID:15196E14-17EB-46A0-AC74-90C3B0F4B8D1-1817-0000019AA7F491AF} -->
