Real-Time Weather Data Pipeline
A comprehensive data engineering solution that collects, processes, and visualizes real-time weather data from the OpenWeatherMap API for Thai provinces and international cities.

Key Features
Automated Data Collection: Fetches weather data for Satitram and 14 nearby locations every 15 minutes.

Workflow Orchestration: Managed by Prefect 3 for reliable, scheduled execution.

Data Versioning: Uses LakeFS for data versioning and lineage tracking.

Bilingual Dashboard: Interactive weather analytics with Thai language support using Streamlit.

Containerized Deployment: Docker-based for consistent environments.

Optimized Storage: Partitioned Parquet format using PyArrow for efficient storage and querying.

Technology Stack
Prefect 3 – Workflow orchestration and scheduling

Docker & Docker Compose – Service containerization

LakeFS – Data lake management and version control

Streamlit – Real-time interactive visualization

Pandas / PyArrow – Data processing and Parquet conversion

Quick Start
1. Clone the Repository
bash
คัดลอก
แก้ไข
git clone https://github.com/wasit7/papapipeline.git
cd papapipeline/tutorial07
2. Install Dependencies
bash
คัดลอก
แก้ไข
pip install -r requirements.txt
3. Set Environment Variables
bash
คัดลอก
แก้ไข
export OPENWEATHER_API_KEY=your-api-key
Edit these in src/pipeline.py, src/setup.py, and visualization/app.py:

python
คัดลอก
แก้ไข
LAKEFS_ENDPOINT = "http://localhost:8000"
LAKEFS_ACCESS_KEY = "your-lakefs-access-key"
LAKEFS_SECRET_KEY = "your-lakefs-secret-key"
LAKEFS_REPO = "student-repo"
LAKEFS_BRANCH = "main"
4. Run Docker
bash
คัดลอก
แก้ไข
docker-compose up -d
5. Deploy Pipeline with Prefect
bash
คัดลอก
แก้ไข
python src/pipeline.py deploy
This creates a deployment named data-pipeline in work pool default-agent-pool running every 15 minutes.

Visualization
Run Streamlit Dashboard
bash
คัดลอก
แก้ไข
cd visualization
streamlit run app.py
Access at: http://localhost:8501

LakeFS Integration
Stores raw and processed data in the main branch of repo student-repo

Browse data at: http://localhost:8000

Project Structure
projectfolder/
├── docker/                      
│   └── Dockerfile
├── make/
│   ├── Dockerfile.jupyter        
│   ├── Dockerfile.prefect-worker 
│   ├── requirements.txt
...
