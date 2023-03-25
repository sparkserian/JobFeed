# JobFeed
A responsive job feed web application that fetches, filters, and displays job listings from a Paylocity API (JSON). 

This is a responsive web application that fetches, filters, and displays job listings from a JSON API. The application is built using HTML, CSS, and JavaScript as the core web technologies. The job data is fetched from an API endpoint that returns JSON-formatted data, which may vary depending on the specific API used.

The HTML structures the content of the web page, and CSS is used for styling, including a dark mode and responsive design. JavaScript is responsible for interactivity and dynamic content manipulation, such as fetching the job data from the API, filtering the job list based on user input, toggling the visibility of job details, and switching between light and dark modes.

The API endpoint used in this project returns job data in JSON format. However, the API endpoint and the data format may be different in certain circumstances, depending on the specific API or job data source being used. In such cases, the JavaScript code responsible for fetching and processing the job data may need to be adjusted accordingly to accommodate the changes in the API endpoint or data format.

The job listings are displayed in a grid layout, with each job posting presented in a square. The grid layout is designed to be responsive and adapts to different screen sizes, adjusting the number of columns and rows to fit the available space.

Please note that this web application uses a fixed API endpoint, and you may need to replace it with your own or a different API endpoint if required. Ensure that the new endpoint provides job data in a compatible JSON format or adjust the JavaScript code to handle the new data format. The code is designed to be modular and easy to understand, with clear comments explaining each section and functionality.

To use this application, simply clone the GitHub repository and open the Job Feed with Filter Git.html file in your web browser. No additional setup is required.

```Javascript 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Job Feed</title>
    <style>
        /* Styling for the toggle container */
        .toggle-container {
    display: flex;
    align-items: center;
}


        /* Styling for the apply button */
        .apply-button {
    display: inline-block;
    background-color: #0B6663;
    color: white;
    text-decoration: none;
    padding: 6px 6px;
    border-radius: 5px;
    font-weight: bold;
    text-align: center;
}
        /* Styling for the body of the webpage */
        body {
            font-family: Arial, sans-serif;
            background-color: #f8f9fa; /* Add a light background color */
            color: #343a40; /* Set a dark text color */
            line-height: 1.5; /* Add some space between lines of text */
            margin: 0;
            padding: 2rem; /* Add some padding around the content */
        }

            /* Styling for the job title (h2) */
        h2 {
            color: #0B6663; /* Set the job title color */
            margin-bottom: 0.5rem; /* Add some space below the job title */
        }

            /* Styling for the paragraphs (p) */
        p {
            margin: 0;
            margin-bottom: 1rem; /* Add some space below paragraphs */
        }

        .job-container {
            border: 1px solid #ccc;
            padding: 1rem;
            margin-bottom: 1rem;
            max-width: 75%; /* Adjusted the maximum width to 30% */
            box-sizing: border-box;
            display: inline-block; /* Changed display to inline-block */
            vertical-align: top;
            margin: 1rem;
            border-radius: 10px; /* Added rounded corners */
        }
            /* Styling for Job title */
        .job-title {
            font-size: 1.5rem;
            font-weight: bold;
        }

            /* Styling for Job details */
        .job-details {
            display: none;
        }

            /* Styling for Job description */
        .job-description {
            white-space: pre-wrap;
        }

        /* Styling for separator */
        .separator {
            width: 100%;
            border-bottom: 1px solid #ccc;
            margin: 1rem 0;
        }

        /* Toggle switch styles */
        .toggle-switch {
            position: relative;
            display: inline-block;
            width: 60px;
            height: 34px;
        }

        .toggle-switch input {
            display: none;
        }

        .slider {
            position: absolute;
            cursor: pointer;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: #ccc;
            transition: .4s;
            border-radius: 34px;
        }
/* add comment */
        .slider:before {
            position: absolute;
            content: "";
            height: 26px;
            width: 26px;
            left: 4px;
            bottom: 4px;
            background-color: white;
            transition: .4s;
            border-radius: 50%;
        }

        input:checked + .slider {
            background-color: #0B6663;
        }

        input:checked + .slider:before {
            transform: translateX(26px);
        }

        .slider::after {
            position: absolute;
            content: "☀️";
            display: block;
            font-size: 14px;
            left: 10px;
            top: 50%;
            transform: translateY(-50%);
            transition: .4s;
        }

        input:checked + .slider::after {
            content: "🌙";
            left: 34px;
        }

        /* Dark mode styles */
        .dark-mode {
            background-color: #343a40;
            color: #f8f9fa;
        }

        .dark-mode h2 {
            color: #f8f9fa;
        }

        .dark-mode .job-container {
            border-color: #6c757d;
            background-color: #495057;
        }
    </style>
</head>
<body>
    <div class="job-feed">
        <!-- Add the toggle switch -->
        <div style="position: fixed; top: 20px; right: 20px; z-index: 100;">
            <div class="toggle-container">
                <span class="toggle-label">Too Bright?</span>
                <label class="toggle-switch">
                    <input type="checkbox" id="toggle-dark-mode">
                    <span class="slider"></span>
                </label>
            </div>
        </div>
        
        

        <!-- Add filter inputs -->
        <div style="margin-bottom: 1rem;">
            <label for="keyword-filter">Keyword:</label>
            <input type="text" id="keyword-filter">
            <label for="location-filter">Location:</label>
            <select id="location-filter">
                <option value="">All Locations</option>
                <option value="Andover, MA">Andover, MA</option>
                <option value="Lawrence, MA">Lawrence, MA</option>
            </select>
            
            
            <button id="apply-filters">Apply Filters</button>
            <button id="reset-filters">Reset Filters</button>

        </div>

        <div class="main-container" style="text-align: center;">
            <div id="job-list"></div>
        </div>
    </div>

    <script>
        let jobs = [];
        
        // Function to fetch jobs from API
        async function fetchJobs() {
            try {
                // Fetch data from the API using the provided URL
                const response = await fetch('API URL HERE');
                
                // Parse the fetched data as JSON
                const jobData = await response.json();
                
                // Assign the jobData.jobs array to the jobs variable
                jobs = jobData.jobs;

                // Get the DOM element with id 'job-list'
                const jobListElement = document.getElementById('job-list');

                // Loop through each job in the jobs array
                jobs.forEach(job => {
                    
                    // Create a new div element to serve as the container for the job information
                    const jobContainer = document.createElement('div');
                    jobContainer.classList.add('job-container');

                    // Create a new h2 element for the job title and add the job title text
                    const jobTitle = document.createElement('h2');
                    jobTitle.classList.add('job-title');
                    jobTitle.textContent = job.title;
                    jobContainer.appendChild(jobTitle);

                    // Create a new button element for displaying the job details and set its text content
                    const jobDetailsButton = document.createElement('button');
                    jobDetailsButton.textContent = 'Job Details';
                    
                    // Add the job details button to the job container
                    jobContainer.appendChild(jobDetailsButton);

                    // Create a new div element to hold the job details information
                    const jobDetails = document.createElement('div');
                    jobDetails.classList.add('job-details');
                    
                    // Add the job details div to the job container
                    jobContainer.appendChild(jobDetails);

                    // Create a new div element for the job description and set its inner HTML content
                    const jobDescription = document.createElement('div');
                    jobDescription.classList.add('job-description');
                    jobDescription.innerHTML = job.description;
                    
                    // Add the job description div to the job details div
                    jobDetails.appendChild(jobDescription);

                    // Create a new paragraph element for the job company information and set its text content
                    const jobCompany = document.createElement('p');
                    jobCompany.textContent = `Company: ${job.companyName}`;
                    
                    // Add the job company paragraph to the job details div
                    jobDetails.appendChild(jobCompany);

                    // Create a new paragraph element for the job location information and set its text content
                    const jobLocation = document.createElement('p');
                    jobLocation.textContent = `Location: ${job.jobLocation.city}, ${job.jobLocation.state}`;
                    
                    // Add the job location paragraph to the job details div
                    jobDetails.appendChild(jobLocation);

                    // Create a new div element to serve as a separator between job details and the apply button
                    const separator = document.createElement('div');
                    separator.classList.add('separator');
                    
                    // Add the separator div to the job container
                    jobContainer.appendChild(separator);

                    // Create a new anchor element for the apply button and set its text content, href, target, and class
                    const applyButton = document.createElement('a');
                    applyButton.textContent = 'Apply Now';
                    applyButton.href = job.applyUrl;
                    applyButton.target = '_blank';
                    applyButton.classList.add('apply-button');
                    
                    // Add the apply button anchor to the job container
                    jobContainer.appendChild(applyButton);

                    // Add the job container to the job list element
                    jobListElement.appendChild(jobContainer);

                    // Add an event listener to the job details button to toggle the display of the job details
                    jobDetailsButton.addEventListener('click', () => {
                        jobDetailsButton.textContent = jobDetails.style.display === 'none' ? 'Hide' : 'Job Details';
                        jobDetails.style.display = jobDetails.style.display === 'none' ? 'block' : 'none';
                    });

                });

            } catch (error) {
                console.error('Error fetching job data:', error);
            }
        }

        function displayJobs(filteredJobs) {
    const jobListElement = document.getElementById('job-list');
    jobListElement.innerHTML = ''; // Clear the existing jobs

    filteredJobs.forEach(job => {
        const jobContainer = document.createElement('div');
        jobContainer.classList.add('job-container');

        const jobTitle = document.createElement('h2');
        jobTitle.classList.add('job-title');
        jobTitle.textContent = job.title;
        jobContainer.appendChild(jobTitle);

        const jobDetailsButton = document.createElement('button');
        jobDetailsButton.textContent = 'Job Details';
        jobContainer.appendChild(jobDetailsButton);

        const jobDetails = document.createElement('div');
        jobDetails.classList.add('job-details');
        jobContainer.appendChild(jobDetails);

        const jobDescription = document.createElement('div');
        jobDescription.classList.add('job-description');
        jobDescription.innerHTML = job.description;
        jobDetails.appendChild(jobDescription);

        const jobCompany = document.createElement('p');
        jobCompany.textContent = `Company: ${job.companyName}`;
        jobDetails.appendChild(jobCompany);

        const jobLocation = document.createElement('p');
        jobLocation.textContent = `Location: ${job.jobLocation.city}, ${job.jobLocation.state}`;
        jobDetails.appendChild(jobLocation);

        const separator = document.createElement('div');
        separator.classList.add('separator');
        jobContainer.appendChild(separator);

        const applyButton = document.createElement('a');
        applyButton.textContent = 'Apply Now';
        applyButton.href = job.applyUrl;
        applyButton.target = '_blank';
        applyButton.classList.add('apply-button');
        jobContainer.appendChild(applyButton);

        jobDetailsButton.addEventListener('click', () => {
            jobDetailsButton.textContent = jobDetails.style.display === 'none' ? 'Hide' : 'Job Details';
            jobDetails.style.display = jobDetails.style.display === 'none' ? 'block' : 'none';
        });



        jobListElement.appendChild(jobContainer); // Moved this line outside of the event listener
    });
}

        function resetFilters() {
            document.getElementById('keyword-filter').value = '';
            document.getElementById('location-filter').value = '';
            applyFilters();
        }

        function applyFilters() {
            const keyword = document.getElementById('keyword-filter').value.toLowerCase();
            const location = document.getElementById('location-filter').value;

            const filteredJobs = jobs.filter(job =>
                (location === '' || `${job.jobLocation.city}, ${job.jobLocation.state}` === location) &&
                (keyword === '' || job.title.toLowerCase().includes(keyword))
            );

            displayJobs(filteredJobs);
        }

        document.getElementById('apply-filters').addEventListener('click', applyFilters);
        document.getElementById('reset-filters').addEventListener('click', resetFilters);

        document.getElementById('toggle-dark-mode').addEventListener('change', (event) => {
            const jobFeed = document.querySelector('.job-feed');
            if (event.target.checked) {
                jobFeed.classList.add('dark-mode');
            } else {
                jobFeed.classList.remove('dark-mode');
            }
        });

        fetchJobs();
    </script>
</body>
</html>
```
