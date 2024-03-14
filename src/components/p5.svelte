<link href="https://api.mapbox.com/mapbox-gl-js/v2.7.0/mapbox-gl.css" rel="stylesheet" />

<script>
    export let data;
    let mostRecentCrimes;

    let crimeText = {
    'Burglary': 'Burglary is a crime of breaking and entering a building illegally with the intent to commit a crime, typically theft.',
    'Arson': 'Arson involves intentionally setting fire to property, often with criminal intent.',
    'Robbery': 'Robbery is the act of taking or attempting to take something from someone by force or threat of force.',
    'Attempted Burglary': 'Attempted Burglary refers to an unsuccessful attempt to break into a building with the intent to commit a crime.',
    'Aggravated Assault': 'Aggravated Assault is a more severe form of assault, often involving a weapon or causing serious injury.',
    'Sexual Battery': 'Sexual Battery is a criminal offense involving non-consensual sexual touching or penetration.',
    'Suspicious Activity': 'Suspicious Activity refers to behavior that raises concerns and may indicate criminal or harmful intent.',
    'Avoid Area': 'Avoid Area warnings suggest staying away from a specific location due to potential danger or ongoing incidents.',
    'Indecent Exposure': 'Indecent Exposure involves the exposure of one\'s genitals in a public place, often with lewd intent.',
    'Motor Vehicle Theft': 'Motor Vehicle Theft is the unauthorized taking of someone\'s vehicle with the intent to permanently deprive them of it.',
    'Sexual Assault': 'Sexual Assault is a broader term that encompasses various non-consensual sexual acts.',
    'Attempted Robbery': 'Attempted Robbery refers to an unsuccessful attempt to take something from someone by force or threat of force.',
    'Weapons Law Violation': 'Weapons Law Violation involves the violation of laws related to the possession or use of weapons.',
    'Brandishing of a Firearm': 'Brandishing of a Firearm is the act of displaying a firearm in a threatening manner.',
    'Attempted Motor Vehicle Theft': 'Attempted Motor Vehicle Theft is an unsuccessful attempt to steal a motor vehicle.',
    'Stalking': 'Stalking is a pattern of unwanted attention, harassment, or other behavior intended to create fear or distress.',
    'Attempted E-Bike / Motor Vehicle Theft': 'Attempted E-Bike / Motor Vehicle Theft is an unsuccessful attempt to steal an e-bike or motor vehicle.',
    '': 'This category may indicate missing or incomplete information about the reported crime.',
    'Intimidation - Sexual Orientation Bias': 'Intimidation - Sexual Orientation Bias involves actions intended to intimidate or harm someone based on their sexual orientation.',
    'Forced Entry Into Occupied Residence': 'Forced Entry Into Occupied Residence refers to entering a residence forcefully while it is occupied.',
    'Attempted Strong-Arm Robbery': 'Attempted Strong-Arm Robbery is an unsuccessful attempt to forcefully take something from someone without a weapon.',
    'Sexual Assault Unknown Perpetrator': 'Sexual Assault by an Unknown Perpetrator involves non-consensual sexual acts where the perpetrator is unidentified.',
    'Information on Jacobs Medical Center Incident': 'This category may contain information about an incident at the Jacobs Medical Center.',
    'Trespassing in an Occupied Residence': 'Trespassing in an Occupied Residence is the unauthorized entry into an occupied residence.',
    'Office Building Burglaries': 'Office Building Burglaries involve unauthorized entry into office buildings with the intent to commit a crime.',
    'Missing Juvenile At Risk': 'Missing Juvenile At Risk indicates the disappearance of a juvenile who may be in danger.',
    'Demonstration': 'A group of people congregating to express opposition to a practice or issue.'
};

    mostRecentCrimes = data.slice(-2);

    let center2 = [mostRecentCrimes[0].longitude, mostRecentCrimes[0].latitude];
    let center1 = [mostRecentCrimes[1].longitude, mostRecentCrimes[1].latitude];

    let date2 = mostRecentCrimes[0].DateOfResponse.split('-').join('/').slice(5)
    let date1 = mostRecentCrimes[1].DateOfResponse.split('-').join('/').slice(5)

    let crime2 = mostRecentCrimes[0].CrimeCategory
    let crime1 = mostRecentCrimes[1].CrimeCategory

    let title2 = `${date2} - ${crime2}`;
    let title1 = `${date1} - ${crime1}`;

    function DescPart1(crimeData) {
        return `Crime Description: ${crimeText[crimeData.CrimeCategory] || 'No description available.'}`;
    }

    function DescPart2(crimeData) {
        return `Reported at: ${crimeData.TimeOfResponse || 'Not available.'}`;
    }

    function DescPart3(crimeData) {
        return `Location: ${crimeData.LocationOfCrime || 'Not available.'}`;
    }

    function DescPart4(crimeData) {
        return `Suspect Description: ${crimeData.SuspectDescription}`;
    }

    function DescPart5(crimeData) {
        return `Suspect in Custody: ${crimeData.SuspectInCustody}`;
    }

    function getPreviousCrimesInArea(currentCrimeData) {
        const mapGroup = currentCrimeData.MapGroup;
        const previousCrimes = data
            .filter(crime => crime.MapGroup === mapGroup && crime.CrimeCategory !== currentCrimeData.CrimeCategory)
            .map(crime => crime.CrimeCategory);

        return [...new Set(previousCrimes)]; // Remove duplicates
    }

    console.log(data)

</script>

<h1 id="subtitle">Recent Warnings</h1>

<main class="container">


    <!-- First Map Container with Title and Description -->
    <div class="map-section">
        <div class="map-info">
            <svg class="map-svg" style="width: 300px; height: 300px;">
                <!-- Use Mapbox Static Images API to generate a static image -->
                <image href={`https://api.mapbox.com/styles/v1/mapbox/navigation-day-v1/static/${center1[0]},${center1[1]},16,0,0/500x500?access_token=pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew`} width="100%" height="100%" />
            </svg>
            <div class="text-section">
                <h2 id="crime-title">{title1}</h2>
                <p id="crime-info">
                    <span>{DescPart1(mostRecentCrimes[1])}</span><br>
                    <span>{DescPart2(mostRecentCrimes[1])}</span><br>
                    <span>{DescPart3(mostRecentCrimes[1])}</span><br>
                    <span>{DescPart4(mostRecentCrimes[1])}</span><br>
                    <span>{DescPart5(mostRecentCrimes[1])}</span><br>
                    <strong>Previous Crimes in this Area:</strong><br>
                    {#each getPreviousCrimesInArea(mostRecentCrimes[1]) as crime}
                        <span>- {crime}</span><br>
                    {/each}
                </p>
                <!-- Add more content as needed -->
            </div>
        </div>
    </div>

    <!-- Second Map Container with Title and Description -->
    <div class="map-section">
        <div class="map-info">
            <svg class="map-svg" style="width: 300px; height: 300px;">
                <!-- Use Mapbox Static Images API to generate a static image -->
                <image href={`https://api.mapbox.com/styles/v1/mapbox/navigation-day-v1/static/${center2[0]},${center2[1]},16,0,0/500x500?access_token=pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew`} width="100%" height="100%" />
            </svg>
            <div class="text-section">
                <h2 id="crime-title">{title2}</h2>
                <p id="crime-info">
                    <span>{DescPart1(mostRecentCrimes[0])}</span><br>
                    <span>{DescPart2(mostRecentCrimes[0])}</span><br>
                    <span>{DescPart3(mostRecentCrimes[0])}</span><br>
                    <span>{DescPart4(mostRecentCrimes[0])}</span><br>
                    <span>{DescPart5(mostRecentCrimes[0])}</span><br>
                    <strong>Previous Crimes in this Area:</strong><br>
                    {#each getPreviousCrimesInArea(mostRecentCrimes[0]) as crime}
                        <span>- {crime}</span><br>
                    {/each}
                </p>
            </div>
        </div>
    </div>

    <div id="top-right-text" >
        Let's quickly take a look at 2 recent examples of Timely Warnings or Community Allert Bulletins that have come out.
        We've provided the date and time of the alert, a description of what the alert means, the name of the location,
        showing the location, and whether there was information regarding a potential suspect. Using previous alerts, we compiled
        a list of crimes that have previously occured in the area. All of this information could be useful in helping students
        make informed decisions and protect their safety.
    </div>

    <style>
        #top-right-text {
            position: absolute;
            top: 140px; 
            left: 900px; 
            right: 60px;
            border: 3px solid #ADD8E6; 
            padding: 10px; 
            background-color: white;
            font-family: 'Lato', sans-serif;
            line-height: 1.3;
            font-size: 18px;
        }

        #subtitle {
            font-size: 28px;
            font-family: 'Roboto', sans-serif;
            margin-left: 180px;
        }

        #crime-title {
            font-family: 'Roboto', sans-serif;
            font-weight: bold;
            margin-left: 15px;
        }

        #crime-info {
            font-family: "EB Garamond", serif;
            font-size: 21px;
            margin-left: 15px;
        }

        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }

        .map-section {
            display: flex;
            width: 100%;
            margin: 10px;
            margin-left: 100px;
        }

        .map-info {
            display: flex;
            width: 50%;
            border: 3px solid #ADD8E6;
            padding: 20px;
            background-color: white;
        }

        .map-svg {
            width: 100%;
        }

        .text-section {
            width: 80%;
            padding: 10px;
            box-sizing: border-box;
        }
    </style>
</main>