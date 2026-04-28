from django.test import TestCase

# Create your tests here.
import pytest
import requests
from datetime import date
from uuid import uuid4

BASE_URL = "http://127.0.0.1:8000/api"


# 🔹 TEST 1: Comprovar que l'API respon
def test_api_root():
    response = requests.get(f"{BASE_URL}/")
    assert response.status_code == 200

# 🔹 TEST 2: Crear un autor
def test_create_autor():
    data = {
        "nom": "Joan",
        "cognoms": "Garcia",
        "nacionalitat": "Espanyola",
        "bio": "Autor de proves"
    }

    response = requests.post(f"{BASE_URL}/autors/", json=data)

    assert response.status_code == 201
    assert response.json()["nom"] == "Joan"

# 🔹 TEST 3: Llistar autors
def test_get_autors():
    response = requests.get(f"{BASE_URL}/autors/")
    
    assert response.status_code == 200
    assert isinstance(response.json(), list)

# 🔹 TEST 4: Crear recurs amb autor
def test_create_recurs():
    # Crear autor
    autor_data = {
        "nom": "Maria",
        "cognoms": "Lopez"
    }

    autor_response = requests.post(f"{BASE_URL}/autors/", json=autor_data)
    assert autor_response.status_code == 201

    autor_id = autor_response.json()["id"]

    # Crear recurs amb títol únic
    titol = f"Recurs Test {uuid4()}"

    recurs_data = {
        "titol": titol,
        "descripcio": "Descripcio test",
        "categoria": "LL",
        "data_publicacio": str(date.today()),
        "is_active": True,
        "autor": autor_id
    }

    response = requests.post(f"{BASE_URL}/recursos/", json=recurs_data)

    # Validacions
    assert response.status_code == 201

    data = response.json()
    assert data["titol"] == titol
    assert data["descripcio"] == "Descripcio test"
    assert data["categoria"] == "LL"
    assert data["autor"] == autor_id

# 🔹 TEST 5: Llistar recursos
def test_get_recursos():
    response = requests.get(f"{BASE_URL}/recursos/")
    
    assert response.status_code == 200
    assert isinstance(response.json(), list)

# 🔹 TEST 6: Error si falta camp obligatori
def test_create_recurs_sense_titol():
    data = {
        "descripcio": "Sense titol",
        "categoria": "LL",
        "data_publicacio": str(date.today())
    }

    response = requests.post(f"{BASE_URL}/recursos/", json=data)

    assert response.status_code == 400

# 🔹 TEST 7: Crear autor sense nom (error)
def test_create_autor_sense_nom():
    data = {
        "cognoms": "SenseNom"
    }

    response = requests.post(f"{BASE_URL}/autors/", json=data)

    assert response.status_code == 400

# 🔹 TEST 8: Crear autor amb nom massa llarg
def test_create_autor_nom_llarg():
    data = {
        "nom": "a" * 200  # max_length = 100
    }

    response = requests.post(f"{BASE_URL}/autors/", json=data)

    assert response.status_code == 400

# 🔹 TEST 9: Crear recurs amb categoria incorrecta
def test_create_recurs_categoria_incorrecta():
    data = {
        "titol": "Recurs Invalid",
        "categoria": "XX",  # no existeix
        "data_publicacio": "2024-01-01"
    }

    response = requests.post(f"{BASE_URL}/recursos/", json=data)

    assert response.status_code == 400

# 🔹 TEST 10: Crear recurs amb data incorrecta
def test_create_recurs_data_incorrecta():
    data = {
        "titol": "Recurs Data Malament",
        "categoria": "LL",
        "data_publicacio": "data_falsa"
    }

    response = requests.post(f"{BASE_URL}/recursos/", json=data)

    assert response.status_code == 400

# 🔹 TEST 11: Crear recurs duplicat (titol unique)
def test_create_recurs_duplicat():
    data = {
        "titol": "Recurs Unic",
        "categoria": "LL",
        "data_publicacio": "2024-01-01"
    }

    # Primer OK
    requests.post(f"{BASE_URL}/recursos/", json=data)

    # Segon ha de fallar
    response = requests.post(f"{BASE_URL}/recursos/", json=data)

    assert response.status_code == 400

# 🔹 TEST 12: Accedir a recurs inexistent
def test_get_recurs_no_existeix():
    response = requests.get(f"{BASE_URL}/recursos/99999/")

    assert response.status_code == 404

# 🔹 TEST 13: Mètode no permès
def test_metode_no_permes():
    response = requests.put(f"{BASE_URL}/autors/")

    assert response.status_code in [400, 405]

# 🔹 TEST 14: Crear recurs amb autor inexistent
def test_create_recurs_autor_inexistent():
    data = {
        "titol": "Recurs Autor Fake",
        "categoria": "LL",
        "data_publicacio": "2024-01-01",
        "autor": 99999
    }

    response = requests.post(f"{BASE_URL}/recursos/", json=data)

    assert response.status_code == 400

# 🔹 TEST 15: Crear recurs buit
def test_create_recurs_buit():
    response = requests.post(f"{BASE_URL}/recursos/", json={})

    assert response.status_code == 400