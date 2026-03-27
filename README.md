# REST-API-JWT
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
from flask_jwt_extended import (
    JWTManager, create_access_token, jwt_required, get_jwt_identity
)

app = Flask(__name__)

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///db.sqlite3'
app.config['JWT_SECRET_KEY'] = 'secret-key'

db = SQLAlchemy(app)
jwt = JWTManager(app)

Flask-JWT-Extended
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True)
    password = db.Column(db.String(80))
    class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    text = db.Column(db.String(200))
    user_id = db.Column(db.Integer)
    @app.route('/register', methods=['POST'])
def register():
    data = request.json

    user = User(username=data['username'], password=data['password'])
    db.session.add(user)
    db.session.commit()

    return jsonify({"msg": "user created"})
    @app.route('/login', methods=['POST'])
def login():
    data = request.json

    user = User.query.filter_by(username=data['username']).first()

    if user and user.password == data['password']:
        token = create_access_token(identity=user.id)
        return jsonify({"token": token})

    return jsonify({"msg": "bad credentials"}), 401
    @app.route('/task', methods=['POST'])
@jwt_required()
def create_task():
    user_id = get_jwt_identity()
    data = request.json

    task = Task(text=data['text'], user_id=user_id)
    db.session.add(task)
    db.session.commit()

    return jsonify({"msg": "task created"})
