import streamlit as st
import mysql.connector

# ---------- Database Connection ----------
def get_connection():
    return mysql.connector.connect(
        host="gdg8b9.h.filess.io",
        user="HOSTEL MANAGEMENT_varietytoy",
        password="6acba73d1ebff27ff1ae1fe06a9e501f1df5e3c6",
        database="HOSTEL MANAGEMENT_varietytoy",
        port=3307
    )

# ---------- Database Operations ----------
def insert_values(sno, name, city, roomno, contact):
    db = get_connection()
    cursor = db.cursor()
    query = "INSERT INTO eagle VALUES (%s, %s, %s, %s, %s)"
    cursor.execute(query, (sno, name, city, roomno, contact))
    db.commit()
    db.close()

def update_values(sno, city, contact):
    db = get_connection()
    cursor = db.cursor()
    query = "UPDATE eagle SET city=%s, contact=%s WHERE sno=%s"
    cursor.execute(query, (city, contact, sno))
    db.commit()
    db.close()

def select_one(sno):
    db = get_connection()
    cursor = db.cursor()
    query = "SELECT * FROM eagle WHERE sno=%s"
    cursor.execute(query, (sno,))
    result = cursor.fetchone()
    db.close()
    return result

def delete_by_sno(sno):
    db = get_connection()
    cursor = db.cursor()
    query = "DELETE FROM eagle WHERE sno=%s"
    cursor.execute(query, (sno,))
    db.commit()
    db.close()

# ---------- Streamlit Frontend ----------
st.set_page_config(page_title="Hostel Management", page_icon="🏠")

st.title("🏠 Hostel Management System")

menu = ["Insert", "Update", "Select", "Delete"]
choice = st.sidebar.selectbox("Menu", menu)

# INSERT RECORD
if choice == "Insert":
    st.subheader("➕ Insert New Record")
    sno = st.number_input("Enter S.No", step=1)
    name = st.text_input("Enter Name")
    city = st.text_input("Enter City")
    roomno = st.text_input("Enter Room No")
    contact = st.text_input("Enter Contact")

    if st.button("Insert Record"):
        try:
            insert_values(sno, name, city, roomno, contact)
            st.success("✅ Record inserted successfully!")
        except Exception as e:
            st.error(f"❌ Error: {e}")

# UPDATE RECORD
elif choice == "Update":
    st.subheader("🔄 Update Record")
    sno = st.number_input("Enter S.No of record to update", step=1)
    city = st.text_input("Enter New City")
    contact = st.text_input("Enter New Contact")

    if st.button("Update Record"):
        try:
            update_values(sno, city, contact)
            st.success("✅ Record updated successfully!")
        except Exception as e:
            st.error(f"❌ Error: {e}")

# SELECT RECORD
elif choice == "Select":
    st.subheader("🔍 View Record by S.No")
    sno = st.number_input("Enter S.No", step=1)

    if st.button("Fetch Record"):
        try:
            result = select_one(sno)
            if result:
                st.table([{
                    "S.No": result[0],
                    "Name": result[1],
                    "City": result[2],
                    "Room No": result[3],
                    "Contact": result[4]
                }])
            else:
                st.warning("⚠️ No record found.")
        except Exception as e:
            st.error(f"❌ Error: {e}")

# DELETE RECORD
elif choice == "Delete":
    st.subheader("🗑️ Delete Record")
    sno = st.number_input("Enter S.No to delete", step=1)

    if st.button("Delete Record"):
        try:
            delete_by_sno(sno)
            st.success("✅ Record deleted successfully!")
        except Exception as e:
            st.error(f"❌ Error: {e}")
