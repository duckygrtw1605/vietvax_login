from flask import Flask, render_template, request, session, redirect, url_for, g
from flask_sqlalchemy import SQLAlchemy
from datetime import datetime

app = Flask(__name__)
app.secret_key = 'abc'

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///user_info.db'
db = SQLAlchemy(app)

@app.before_request
def before_request():
    g.user = None

    if 'user_id' in session:
        user = [x for x in users if x.id == session['user_id']][0]
        g.user = user

class VietVax(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    date_created = db.Column(db.DateTime, default=datetime.utcnow)

class User:
    def __init(self,id, username, password):
        self.id = id
        self.username = username
        self.password = password

    def __repr__(self):
        return f'<User: {self.username}>'    

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        session.pop('user_id', None)

        username = request.form['username']
        password = request.form['password'] 

        user = [x for x in users if x.username == username][0]
        if user and user.password == password:
            session['user_id'] = user.id
            return redirect(url_for('profile'))
        
        return render_template(url_for('login.html'))

    return render_template('login.html')

@app.route('/profile')
def profile():
    if not g.user:
        return render_template(url_for('login.html'))
    return render_template('profile.html')

if __name__ == "__main__":
    app.run(debug=True)
