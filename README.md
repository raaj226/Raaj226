import streamlit as st
import pandas as pd
import os

# ایپ کا ٹائٹل اور سیٹنگ
st.set_page_config(page_title="Tailor Business Manager", layout="wide")
st.title("🧵 درزی بزنس مینجمنٹ ایپ")

# ڈیٹا بیس فائل کا نام
DB_FILE = "tailor_data.csv"

# ڈیٹا لوڈ کرنے کا فنکشن
def load_data():
    if os.path.exists(DB_FILE):
        return pd.read_csv(DB_FILE)
    return pd.DataFrame(columns=["نام", "فون نمبر", "لمبائی", "کندھا", "بازو", "شلوار", "کل رقم", "ایڈوانس", "اسٹیٹس"])

data = load_data()

# سائیڈ بار مینیو
menu = ["نیا آرڈر درج کریں", "تمام آرڈرز دیکھیں", "آرڈر سرچ کریں"]
choice = st.sidebar.selectbox("مینیو منتخب کریں", menu)

if choice == "نیا آرڈر درج کریں":
    st.header("گاہک کی پیمائش اور تفصیلات")
    
    with st.form("measurement_form"):
        col1, col2 = st.columns(2)
        with col1:
            name = st.text_input("گاہک کا نام")
            phone = st.text_input("فون نمبر")
            length = st.text_input("لمبائی (Length)")
            shoulder = st.text_input("کندھا (Shoulder)")
        with col2:
            arms = st.text_input("بازو (Arms)")
            pants = st.text_input("شلوار / پینٹ")
            price = st.number_input("کل رقم", min_value=0)
            advance = st.number_input("ایڈوانس رقم", min_value=0)
        
        status = st.selectbox("آرڈر اسٹیٹس", ["پینڈنگ (Pending)", "سلائی جاری ہے (In Progress)", "مکمل (Ready)"])
        
        submit = st.form_submit_button("آرڈر محفوظ کریں")
        
        if submit:
            new_data = pd.DataFrame([[name, phone, length, shoulder, arms, pants, price, advance, status]], 
                                    columns=data.columns)
            data = pd.concat([data, new_data], ignore_index=True)
            data.to_csv(DB_FILE, index=False)
            st.success(f"{name} کا آرڈر کامیابی سے محفوظ ہو گیا!")

elif choice == "تمام آرڈرز دیکھیں":
    st.header("تمام ریکارڈز")
    if not data.empty:
        st.dataframe(data)
    else:
        st.warning("فی الحال کوئی ریکارڈ موجود نہیں ہے۔")

elif choice == "آرڈر سرچ کریں":
    st.header("گاہک تلاش کریں")
    search_name = st.text_input("نام لکھیں")
    if search_name:
        result = data[data['نام'].str.contains(search_name, case=False, na=False)]
        st.write(result)
        
