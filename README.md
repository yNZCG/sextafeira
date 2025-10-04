from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time

def main():
    url = "https://investidor10.com.br/acoes/petr4/"
    chromedriver_path = r"./chromedriver.exe"

    options = Options()
    # Remova o modo “headless” para ver o navegador carregar (útil para depuração)
    # options.add_argument("--headless")
    options.add_argument("--disable-gpu")
    options.add_argument("--no-sandbox")

    service = Service(chromedriver_path)
    driver = webdriver.Chrome(service=service, options=options)

    try:
        print("Abrindo página:", url)
        driver.get(url)

        # Espera inicial
        time.sleep(3)

        # Role para baixo para ativar o carregamento dinâmico
        driver.execute_script("window.scrollTo(0, 400)")
        time.sleep(3)

        # Espera explícita até que o elemento com ID “acao_valor” ou similar apareça
        wait = WebDriverWait(driver, 20)
        # Tente localizar pelo ID "acao_valor"
        preco_element = wait.until(EC.visibility_of_element_located((By.ID, "acao_valor")))
        preco = preco_element.text.strip()
        print("Valor encontrado via ID 'acao_valor':", preco)

    except Exception as e_id:
        print("Erro ao localizar por ID:", e_id)
        # Se falhar com ID, tente localizar por classe ou outro seletor
        try:
            # Exemplo alternativo: buscar por algum elemento <span> com classe “cotacao”
            preco_alt = driver.find_element(By.CSS_SELECTOR, "span.cotacao, .valor, .price")
            preco2 = preco_alt.text.strip()
            print("Valor encontrado por seletor alternativo:", preco2)
        except Exception as e_alt:
            print("Também não conseguiu obter o valor por seletor alternativo:", e_alt)

    finally:
        driver.quit()

if __name__ == "__main__":
    main()
