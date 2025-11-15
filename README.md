# daiane_projeto

from sqlalchemy import Column, Integer, String, Float, ForeignKey, DateTime
from sqlalchemy.orm import declarative_base, relationship
from datetime import datetime

Base = declarative_base()

class Usuario(Base):
    __tablename__ = "usuarios"

    id = Column(Integer, primary_key=True)
    nome = Column(String(80))
    email = Column(String(120), unique=True)
    senha = Column(String(200))


class Cliente(Base):
    __tablename__ = "clientes"

    id = Column(Integer, primary_key=True)
    nome = Column(String(100))
    telefone = Column(String(20))
    endereco = Column(String(200))


class Produto(Base):
    __tablename__ = "produtos"

    id = Column(Integer, primary_key=True)
    nome = Column(String(100))
    preco = Column(Float)


class Venda(Base):
    __tablename__ = "vendas"

    id = Column(Integer, primary_key=True)
    cliente_id = Column(Integer, ForeignKey("clientes.id"))
    data = Column(DateTime, default=datetime.utcnow)

    cliente = relationship("Cliente")
    itens = relationship("ItemVenda", back_populates="venda")


class ItemVenda(Base):
    __tablename__ = "itens_venda"

    id = Column(Integer, primary_key=True)
    venda_id = Column(Integer, ForeignKey("vendas.id"))
    produto_id = Column(Integer, ForeignKey("produtos.id"))
    quantidade = Column(Integer)
    subtotal = Column(Float)

    venda = relationship("Venda", back_populates="itens")
    produto = relationship("Produto")

                                                                                                                                                                                                                                                email = Column(String(120), unique=True)
from datetime import datetime
from sqlalchemy.orm import Session
from models import Usuario, Cliente, Produto, Venda, ItemVenda

class BaseDAO:
    def __init__(self, model, session: Session):
        self.model = model
        self.session = session

    def inserir(self, obj):
        self.session.add(obj)
        self.session.commit()

    def listar(self):
        return self.session.query(self.model).all()

    def procurar(self, id):
        return self.session.query(self.model).get(id)

    def alterar(self, id, dados: dict):
        obj = self.procurar(id)
        for campo, valor in dados.items():
            setattr(obj, campo, valor)
        self.session.commit()

    def apagar(self, id):
        obj = self.procurar(id)
        self.session.delete(obj)
        self.session.commit()


class UsuarioDAO(BaseDAO):

    def autenticar(self, email, senha):
        return self.session.query(Usuario).filter_by(
            email=email, senha=senha
        ).first()from fastapi import FastAPI
from database import SessionLocal
from dao import UsuarioDAO
from models import Usuario

app = FastAPI()

@app.get("/usuario/{id}")
def get_usuario(id: int):
    session = SessionLocal()
    dao = UsuarioDAO(Usuario, session)
    usuario = dao.procurar(id)

    if not usuario:
        return {"erro": "Usuário não encontrado"}

    return {
        "id": usuario.id,
        "nome": usuario.nome,
        "email": usuario.email
    }git add .
git commit -m "Implementação do banco de dados, models, DAO e controller"
git push origin banco-de-dados