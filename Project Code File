import requests

# OpenVAS API URL and credentials
API_URL = "https://your-openvas-server:9392"
API_USERNAME = "your-username"
API_PASSWORD = "your-password"

# Create a session
session = requests.Session()
session.auth = (API_USERNAME, API_PASSWORD)
session.verify = False  # Ignore SSL certificate verification (not recommended for production)

# Get a list of report formats
def get_report_formats():
    url = f"{API_URL}/omp?cmd=get_report_formats"
    response = session.get(url)
    formats = response.json()["report_format"]
    return formats

# Launch a scan and get the report
def generate_report(scan_id, report_format_id):
    url = f"{API_URL}/omp?cmd=launch_report&report_id={report_format_id}&scan_id={scan_id}"
    response = session.get(url)
    report_id = response.json()["report_id"]

    # Wait for the report to finish
    while True:
        url = f"{API_URL}/omp?cmd=get_report&report_id={report_id}"
        response = session.get(url)
        status = response.json()["status"]

        if status == "Done":
            download_url = response.json()["report"]["report_download"]
            report_data = session.get(download_url).content
            return report_data
        elif status == "Running":
            continue
        else:
            return None

# Example usage
def main():
    # Get scan results
    scan_id = "scan-id-from-openvas"
    report_format_id = "report-format-id"  # Choose a report format from get_report_formats()

    report_data = generate_report(scan_id, report_format_id)

    if report_data:
        with open("scan_report.pdf", "wb") as f:
            f.write(report_data)
        print("Report generated successfully.")
    else:
        print("Failed to generate the report.")

if __name__ == "__main__":
    main()
