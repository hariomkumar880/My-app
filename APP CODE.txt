import streamlit as st
import pandas as pd
import plotly.express as px

# Page Config
st.set_page_config(page_title="Empower AI Dashboard", page_icon="📊", layout="wide")

# Title
st.title("📊 Empower AI: Transformative Learning Dashboard")
st.write("A CSR Initiative by Microsoft & SAP")

# Upload Data
uploaded_file = st.file_uploader("Upload CSV or Excel file", type=["csv", "xlsx"])
if uploaded_file:
    try:
        df = pd.read_csv(uploaded_file) if uploaded_file.name.endswith(".csv") else pd.read_excel(uploaded_file)
        st.success("File uploaded successfully!")
    except Exception as e:
        st.error(f"Error loading file: {e}")
        st.stop()

    # Show Data
    st.subheader("Dataset Preview")
    st.dataframe(df.head())

    # Data Summary
    st.subheader("Dataset Summary")
    st.write(df.describe())

    # Select Columns
    numeric_columns = df.select_dtypes(include=['number']).columns.tolist()
    categorical_columns = df.select_dtypes(include=['object']).columns.tolist()

    # Visualization
    st.subheader("📈 Data Visualization")
    chart_type = st.selectbox("Select Chart Type", ["Bar Chart", "Line Chart", "Pie Chart"])
    
    if chart_type == "Bar Chart":
        x_axis = st.selectbox("Select X-axis", categorical_columns)
        y_axis = st.selectbox("Select Y-axis", numeric_columns)
        fig = px.bar(df, x=x_axis, y=y_axis, title=f"{x_axis} vs {y_axis}")
        st.plotly_chart(fig)
    
    elif chart_type == "Line Chart":
        x_axis = st.selectbox("Select X-axis", categorical_columns)
        y_axis = st.selectbox("Select Y-axis", numeric_columns)
        fig = px.line(df, x=x_axis, y=y_axis, title=f"{x_axis} vs {y_axis}")
        st.plotly_chart(fig)
    
    elif chart_type == "Pie Chart":
        category = st.selectbox("Select Category", categorical_columns)
        value = st.selectbox("Select Value", numeric_columns)
        fig = px.pie(df, names=category, values=value, title=f"{category} Distribution")
        st.plotly_chart(fig)

    # Download Processed Data
    st.subheader("⬇ Download Processed Data")
    st.download_button("Download CSV", df.to_csv(index=False).encode('utf-8'), "processed_data.csv", "text/csv")
