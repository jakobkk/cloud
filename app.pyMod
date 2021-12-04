'''
    Design an interactive web page to do the following: (Points: 5, broken as follows)
        Create a registration page with (store it): (Points: 1/5)
            Username
            Password
        Accept users basic details (store it): (Points: 1/5)
            First name
            Last name
            Email
        Upon submission redirect to next page (Points: 1/5)
            Display information accepted in step 3b
        Should ask for username and password to retrieve user information. (Points: 2/5)

Extra Credit (Points : 3)

    Upload the file "Limerick.txt" with above designed form.   Limerick.txt Download Limerick.txt  
    Store it.
    Display the count of words in the file on page designed in step#3c and provide link to download it.
    Display all the information from extra credit step#3 upon relogin.

'''


from flask import Flask, render_template, request, url_for, redirect, send_from_directory
from flask_sqlalchemy import SQLAlchemy
from flask_login import LoginManager, UserMixin, login_user, logout_user, current_user, login_required

app = Flask(__name__, static_folder='data')

app.config['SQLALCHEMY_DATABASE_URI'] = r'sqlite:///test.db'
app.config['SECRET_KEY'] = b'gfdgqerhq3'

db = SQLAlchemy(app)
login_manager = LoginManager()
login_manager.init_app(app)

class User(UserMixin, db.Model):
    id = db.Column(db.Integer, primary_key=True)
    uname = db.Column(db.String(30), unique=True)
    fname = db.Column(db.String(30))
    lname = db.Column(db.String(30))
    email = db.Column(db.String(30))
    pword = db.Column(db.String(30))


@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))

@app.route('/',methods=['GET','POST'])
@app.route('/login',methods=['GET','POST'])
def login():
    logout_user()
    if request.method == 'POST':
        userName = request.form['username']
        iUser = User.query.filter_by(uname=userName).first()
        if iUser is not None:
            password = request.form['password']
            if password == iUser.pword:
                login_user(iUser)
                return redirect(url_for('hello'))
            else:
                return render_template('login.html', error="Invalid login")

        else:
            return render_template('login.html', error='User Not Found') 
        
    return render_template('login.html')

@app.route('/register',methods = ['GET','POST'])
def register():
    if request.method == "POST":
        wellForm = True
        errorStr = 'Invalid '
        if len(request.form['username']) < 1 or len(request.form['username']) > 30:
            wellForm = False
            errorStr += 'username, '
        elif len(request.form['firstname']) < 1 or len(request.form['firstname']) > 30:
            wellForm = False
            errorStr += 'first name,  '
        elif len(request.form['lastname']) < 1 or len(request.form['lastname']) > 30:
            errorStr += 'last name,  '
            wellForm = False
        elif len(request.form['email']) < 1 or len(request.form['email']) > 30:
            errorStr += 'email,  '
            wellForm = False
        elif len(request.form['password']) < 1 or len(request.form['password']) > 30:
            errorStr += 'password,  '
            wellForm = False
        elif User.query.filter_by(uname=request.form['username']).first() is not None:
            errorStr = 'User already exists'
            wellForm = False
        if wellForm:
            h = User(uname=request.form['username'],\
                fname=request.form['firstname'],lname=request.form['lastname'],\
                email=request.form['email'],pword=request.form['password'])
            db.session.add(h)
            db.session.commit()
            return redirect(url_for('login'))

        else:
           return render_template('register.html', error=errorStr) 
    return render_template('register.html')

@app.route('/hello', methods = ['GET', 'POST'])
@login_required
def hello():
    if request.method =='POST':
        return send_from_directory(directory='///data', filename='Limerick.txt')
    return render_template('hello.html', fname=current_user.fname, lname=current_user.lname,email=current_user.email)

if __name__ == '__main__':
    db.create_all()
    h = User(id = 1, uname='h',fname='h',lname='h',email='h',pword='h')
    db.session.add(h)
    db.session.commit()
    app.run(host='0.0.0.0',port=81)