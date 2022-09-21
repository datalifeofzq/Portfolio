/*
Covid 19 Data Exploration 
Skills used: Joins, CTE's, Temp Tables, Windows Functions, Aggregate Functions, Creating Views, Converting Data Types
*/

SELECT 
 *
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths
WHERE continent IS NOT NULL 
ORDER BY 3,4


-- Select Data that we are going to be starting with

Select 
 location, 
 date, 
 total_cases, 
 new_cases, 
 total_deaths, 
 population
From [Portfolio Project #1 (COVID)]..Covid_Deaths
Where continent IS NOT NULL 
ORDER BY 1,2


-- Total Cases vs Total Deaths
-- Shows likelihood of dying if you contract covid in your country

SELECT
 location, 
 date, 
 total_cases,
 total_deaths, 
 (total_deaths/total_cases)*100 AS Death_Percentage
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths
WHERE continent IS NOT NULL
ORDER BY 1,2


-- Total Cases vs Population
-- Shows what percentage of population infected with Covid

SELECT 
 location, 
 date, 
 population, 
 total_cases,  
 (total_cases/population)*100 AS Percent_Population_Infected
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths
ORDER BY 1,2


-- Countries with Highest Infection Rate compared to Population

SELECT 
 location, 
 population, 
 MAX(total_cases) AS Highest_Infection_Count,  
 MAX((total_cases/population))*100 AS Percent_Population_Infected
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths
GROUP BY location, population
ORDER BY 4 DESC


-- Countries with Highest Death Count per Population

SELECT 
 location, 
 MAX(CAST(total_deaths AS INT)) AS Total_Death_Count
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths 
WHERE continent IS NOT NULL
GROUP BY location
ORDER BY 2 DESC


-- BREAKING THINGS DOWN BY CONTINENT

-- Showing contintents with the highest death count per population

SELECT 
 continent, 
 MAX(CAST(Total_deaths AS INT)) as Total_Death_Count
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths
Where continent IS NOT NULL
GROUP BY continent
ORDER BY 2 DESC


-- GLOBAL NUMBERS

SELECT 
 SUM(new_cases) AS total_cases, 
 SUM(CAST(new_deaths AS INT)) AS total_deaths, 
 SUM(CAST(new_deaths AS INT))/SUM(New_Cases)*100 as Death_Percentage
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths
WHERE continent IS NOT NULL
ORDER BY 1,2


-- Total Population vs Vaccinations
-- Shows Percentage of Population that has recieved at least one Covid Vaccine

SELECT 
 dea.continent, 
 dea.location, 
 dea.date, 
 dea.population, 
 vac.new_vaccinations, 
 SUM(CONVERT(BIGINT,vac.new_vaccinations)) OVER (PARTITION BY dea.Location ORDER BY dea.location, dea.Date) AS RollingPeopleVaccinated
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths dea
JOIN [Portfolio Project #1 (COVID)]..Covid_Vaccinations vac
 ON dea.location = vac.location
 AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
ORDER BY 2,3


-- Using CTE to perform Calculation on Partition By in previous query

WITH PopvsVac (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated) AS
(
SELECT 
 dea.continent, 
 dea.location, 
 dea.date, 
 dea.population, 
 vac.new_vaccinations, 
 SUM(CAST(vac.new_vaccinations AS BIGINT)) OVER (PARTITION BY dea.Location ORDER BY dea.location, dea.Date) AS RollingPeopleVaccinated
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths dea
JOIN [Portfolio Project #1 (COVID)]..Covid_Vaccinations vac
 ON dea.location = vac.location
 AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
)
Select 
 *, 
 (RollingPeopleVaccinated/Population)*100
FROM PopvsVac


-- Using Temp Table to perform Calculation on Partition By in previous query

DROP TABLE IF EXISTS #PercentPopulationVaccinated
CREATE TABLE #PercentPopulationVaccinated
(
Continent NVARCHAR(255),
Location NVARCHAR(255),
Date DATETIME,
Population NUMERIC,
New_vaccinations BIGINT,
RollingPeopleVaccinated NUMERIC
)
INSERT INTO #PercentPopulationVaccinated
SELECT 
 dea.continent, 
 dea.location, 
 dea.date, 
 dea.population, 
 vac.new_vaccinations, 
 SUM(CAST(vac.new_vaccinations AS BIGINT)) OVER (PARTITION BY dea.Location ORDER BY dea.location, dea.Date) AS RollingPeopleVaccinated
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths dea
JOIN [Portfolio Project #1 (COVID)]..Covid_Vaccinations vac
 ON dea.location = vac.location
 AND dea.date = vac.date

SELECT 
 *, 
 (RollingPeopleVaccinated/Population)*100
FROM #PercentPopulationVaccinated


-- Creating View to store data for later visualizations

CREATE VIEW PercentPopulationVaccinated AS
SELECT 
 dea.continent, 
 dea.location, 
 dea.date, 
 dea.population, 
 vac.new_vaccinations, 
 SUM(CAST(vac.new_vaccinations AS BIGINT)) OVER (PARTITION BY dea.Location ORDER BY dea.location, dea.Date) AS RollingPeopleVaccinated
FROM [Portfolio Project #1 (COVID)]..Covid_Deaths dea
JOIN [Portfolio Project #1 (COVID)]..Covid_Vaccinations vac
 ON dea.location = vac.location
 AND dea.date = vac.date
WHERE dea.continent IS NOT NULL
