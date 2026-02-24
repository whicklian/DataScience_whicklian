Install System Dependencies (PortAudio + Python Dev Headers)
sudo apt update
sudo apt install portaudio19-dev python3-devy


---

## ** Make sure your virtual environment is activated**

You already have `(venv)` in your prompt, so that’s good.

---

## **  Reinstall or bootstrap pip inside the venv**

Run:

```bash
python -m ensurepip --upgrade
```

This will install `pip` inside your virtual environment.

---

## ** Upgrade pip**

```bash
python -m pip install --upgrade pip
```

---

## ** Install Streamlit**

```bash
pip install streamlit
```

* After this, you can verify:

```bash
streamlit --version
```

* It should print the version installed.

---

## ** Run Your App**

```bash
streamlit run voice_ml_demo.py
```

---

### Notes
