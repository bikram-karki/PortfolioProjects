select * 
from PortfolioProject..CovidDeaths
where continent is not NULL
order by 3,4

--looking at Total Cases vs Total Deaths

select location, date, total_cases, total_deaths, (total_deaths/total_cases) * 100 as DeathPercentage
from PortfolioProject..CovidDeaths
where location like '%Afg%'
order by 1,2

--looking at Total cases vs population


select location, date, total_cases, population, (total_cases/population) * 100 as InfectedPercentage
from PortfolioProject..CovidDeaths
order by 1,2

-- looking at countries with highest infected rates compared to population

select location, population, MAX(total_cases) as HighestInfectionCount, MAX((total_cases/population)) * 100 as PopulationPercentInfected
from PortfolioProject..CovidDeaths
group by [location], population
order by HighestInfectionCount desc

-- Showing countries with highest death count per population

select location, MAX(cast(total_deaths as int)) as TotalDeathCount
from PortfolioProject..CovidDeaths
where continent is not NULL
group by location
order by totalDeathCount desc

--LETS BREAK THINGS DOWN BY CONTINENT


-- Showing continents with highest death counts

select continent, MAX(cast(total_deaths as int)) as TotalDeathCount
from PortfolioProject..CovidDeaths
where continent is not NULL
group by continent
order by totalDeathCount desc


-- GLOBAL NUMBERS

-- Global Numbers everyday

select date, sum(new_cases) as Total_Cases, sum(cast(new_deaths as int)) as Total_Deaths, sum(cast(new_deaths as int))/sum(new_cases) * 100 as DeathPercentage --total_cases, total_deaths, (total_deaths/total_cases) * 100 as DeathPercentage
from PortfolioProject..CovidDeaths
where continent is not NULL
GROUP by [date]
order by 1,2

-- Global numbers overall

select sum(new_cases) as Total_Cases, sum(cast(new_deaths as int)) as Total_Deaths, sum(cast(new_deaths as int))/sum(new_cases) * 100 as DeathPercentage --total_cases, total_deaths, (total_deaths/total_cases) * 100 as DeathPercentage
from PortfolioProject..CovidDeaths
where continent is not NULL
--GROUP by [date]
order by 1,2

-- Looking at Total Population vs Vaccinations


SELECT dea.continent, dea.[location], dea.date, dea.population, vac.new_vaccinations_smoothed_per_million
, sum(convert(int,vac.new_vaccinations_smoothed_per_million)) OVER (partition by dea.location order by dea.location, dea.date) as RollingPeopleVaccinated
from PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
    ON dea.[location] = vac.[location]
    and dea.date = vac.date
where dea.continent is not null
order by 2,3

-- USE CTE

With PopvsVac (Continent, Location, Date, Population, New_vaccinations_smoothed_per_million, RollingPeopleVaccinated)
as
(
SELECT dea.continent, dea.[location], dea.date, dea.population, vac.new_vaccinations_smoothed_per_million
, sum(convert(int,vac.new_vaccinations_smoothed_per_million)) OVER (partition by dea.location) as RollingPeopleVaccinated
from PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
    ON dea.[location] = vac.[location]
    and dea.date = vac.date
where dea.continent is not null

)
SELECT *, (RollingPeopleVaccinated/Population) * 100
from PopvsVac

-- TEMP TABLE


Drop table if EXISTS #PercentPopulationVaccinated
CREATE table #PercentPopulationVaccinated
(
    Continent nvarchar(255),
    Location NVARCHAR(255),
    date DATETIME,
    population NUMERIC,
    new_vaccinations_smoothed_per_million NUMERIC,
    rollingpeoplevaccinated numeric
)

Insert into #PercentPopulationVaccinated
SELECT dea.continent, dea.[location], dea.date, dea.population, vac.new_vaccinations_smoothed_per_million
, sum(convert(int,vac.new_vaccinations_smoothed_per_million)) OVER (partition by dea.location) as RollingPeopleVaccinated
from PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
    ON dea.[location] = vac.[location]
    and dea.date = vac.date
where dea.continent is not null

SELECT *, (RollingPeopleVaccinated/Population)*100
from #PercentPopulationVaccinated


-- Creating View to store data for later visualizations

Create View PercentPopulationVaccinated as
SELECT dea.continent, dea.[location], dea.date, dea.population, vac.new_vaccinations_smoothed_per_million
, sum(convert(int,vac.new_vaccinations_smoothed_per_million)) OVER (partition by dea.location) as RollingPeopleVaccinated
from PortfolioProject..CovidDeaths dea
Join PortfolioProject..CovidVaccinations vac
    ON dea.[location] = vac.[location]
    and dea.date = vac.date
where dea.continent is not null
