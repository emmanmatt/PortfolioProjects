Select *
From [dbo].[CovidDeaths$]


--Select *
--From [dbo].[CovidVaccination]
--Order by 3,4

--Select data that we are going to be using

Select location, date, total_cases, new_cases, total_deaths, population
From [dbo].[CovidDeaths$]
Where continent is not null
order by 1,2

--Looking at total cases vrs total deaths
--Shows the likelihood of dying from contracting Covid in Canada

Select location, date, total_cases,  total_deaths, (Convert (Decimal(15, 3), total_deaths)/Convert (Decimal(15, 3),total_cases)*100) as DeathPercentage
From [dbo].[CovidDeaths$]
Where location = 'Canada' and Where continent is not null
order by 1,2

--Total cases vrs population
--What percentation of population contracted covid

Select location, date, population, total_cases, (total_cases/population)*100 as ContractionPercentage --(Convert (Decimal(15, 3), total_deaths)/Convert (Decimal(15, 3),total_cases)*100) as DeathPercentage
From [dbo].[CovidDeaths$]
Where location = 'Canada' and Where continent is not null
order by 1,2

--Countries with highest infection rate compared to population

Select location, population, Max(total_cases) as HighesInfectionCOunt, Max((total_cases/population)*100) as PercentofPopulationInfected
From [dbo].[CovidDeaths$]
Where continent is not null
--Where location = 'Canada'
Group by location, Population
order by PercentofPopulationInfected desc

--Showing countries with highest deathccount per population

Select location, Max(cast(total_deaths as int)) as TotalDeathCount
From [dbo].[CovidDeaths$]
Where continent is not null
--Where location = 'Canada'
Group by location
order by TotalDeathCount desc

--lets do same thing by continent
Select continent, Max(cast(total_deaths as int)) as TotalDeathCount
From [dbo].[CovidDeaths$]
Where continent is not null
--Where location = 'Canada'
Group by continent
order by TotalDeathCount desc

--Showing the continents with the highest deathCount per Population
Select continent, max(cast(total_deaths as int)) as TotalDeathCount
From [dbo].[CovidDeaths$]
WHere continent is not null
Group by continent
order by TotalDeathCount desc



--Global Numbers
Select SUM(new_cases) as TotalNewCases, SUM(Cast(new_deaths as int)) as TotalNewDeaths, SUM(Cast(new_deaths as int))/Nullif(Sum(new_cases),0)*100 as DeathPercentage
From [dbo].[CovidDeaths$]
Where continent is not null
--Group by date
Order by 1,2

--Joining the two Tables
Select *
From [dbo].[CovidDeaths$] dea inner join [dbo].[CovidVaccination$] vac 
	on dea.location= vac.location
	and dea.date=vac.date

	--Looking at Total Population vs Vacinnation

Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(Cast(vac.new_vaccinations as bigint)) OVER (Partition by dea.location order by dea.location, dea.date) as RollingPeopleVaccinatedorMovingTotal, 
	--(RollingPeopleVaccinatedorMovingTotal/Population)*100-- we want to add this but because it is a calculated field it would give an error so we create a CTE
-- you want it to be a moving total
--or YOU CAN USE SUM(CONVERT (BIGINT, vac.new_vaccinations) to replace the SUM(CAST part
From [dbo].[CovidDeaths$] dea inner join [dbo].[CovidVaccination$] vac 
	on dea.location= vac.location
	and dea.date=vac.date
Where dea.continent is not null
	Order by 2,3

--CTE
With Populationvsvaccinnation (continent,  location, date, population, new_vaccinations, RollingPeopleVaccinatedorMovingTotal)
--the columns of the CTE (Populationvsvaccinnation) must be the same as our select statement which will come downwards
as 
(
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(Cast(vac.new_vaccinations as bigint)) OVER (Partition by dea.location order by dea.location, dea.date) as RollingPeopleVaccinatedorMovingTotal 
From [dbo].[CovidDeaths$] dea inner join [dbo].[CovidVaccination$] vac 
	on dea.location= vac.location
	and dea.date=vac.date
Where dea.continent is not null
)
	--Order by 2,3 Never include order by

Select *, (RollingPeopleVaccinatedorMovingTotal/Population)*100
From Populationvsvaccinnation --Run everything from the CTE to this point
Where location = 'Albania'



--YOU CAN USE CTE OR TEMP TABLE

--TEMP TABLE
Drop Table if exists #Populationvsvaccinnation
Create Table #Populationvsvaccinnation
(
Continent nvarchar(255),
location nvarchar (255),
Date datetime,
Population numeric,
new_vaccinations numeric,
RollingPeopleVaccinatedorMovingTotal numeric
)
Insert into #Populationvsvaccinnation
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(Cast(vac.new_vaccinations as bigint)) OVER (Partition by dea.location order by dea.location, dea.date) as RollingPeopleVaccinatedorMovingTotal 
From [dbo].[CovidDeaths$] dea inner join [dbo].[CovidVaccination$] vac 
	on dea.location= vac.location
	and dea.date=vac.date
Where dea.continent is not null

Select *, (RollingPeopleVaccinatedorMovingTotal/Population)*100
From #Populationvsvaccinnation --Run everything from the TEMP TABLE to this point

--CREATING VIEWS TO STORE DATA FOR VISUALIZATIONS

Create view PercentagePopulationVaccinated as 
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
SUM(Cast(vac.new_vaccinations as bigint)) OVER (Partition by dea.location order by dea.location, dea.date) as RollingPeopleVaccinatedorMovingTotal 
From [dbo].[CovidDeaths$] dea inner join [dbo].[CovidVaccination$] vac 
	on dea.location= vac.location
	and dea.date=vac.date
Where dea.continent is not null
	--Order by 2,3 Never include order by
