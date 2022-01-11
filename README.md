# Covid_19
/* Covid 19 Data Exploration */

select *
from test.coviddeaths_csv
where continent is not null
order by 3,4

-- select the Data that we are going to use
-- 1
select location_l, date_d, population, total_cases, new_cases, total_deaths
from test.coviddeaths_csv
where continent is not null and location_l = 'India'
order by 1,2

-- Total Cases vs Total Deaths
-- Probability to die if get infected in India
-- 2
select location_l, date_d, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
from test.coviddeaths_csv
where continent is not null and location_l = 'India'
order by 1,2

-- Total cases vs Population
-- Display percentage of infected population
-- 3
select location_l, date_d, population, total_cases, (total_cases/population)*100 as PercentagePopulationInfected
from test.coviddeaths_csv
where continent is not null and location_l = 'India'
order by 1,2 desc

-- Highest infection rate country against their population
Select location_l, population, MAX(total_cases) as HighestInfectionCount, Max((total_cases/population))*100 as PercentagePopulationInfected
From test.coviddeaths_csv
Group by location_l, population
order by PercentagePopulationInfected desc

-- Highest death rate country against their population
select location_l, max(total_deaths) as TotalDeathCount 
from test.coviddeaths_csv
where continent is not null
group by location_l
order by TotalDeathCount desc

-- Total deaths filter by continent

-- select location_l, max(total_deaths) as TotalDeathCount 
-- from test.coviddeaths_csv
-- where continent is null and location_l not in ('World','High income','Upper middle income','Lower middle income','European union', 'Low income','International')
-- group by location_l
-- order by TotalDeathCount desc

select continent, sum(Deaths) as TotalDeathCount
from (select continent, max(total_deaths) as Deaths
from test.coviddeaths_csv
where continent is not null
group by location_l) as ByContinent
group by continent
order by TotalDeathCount desc

-- Global Numbers
Select SUM(new_cases) as total_cases, SUM(new_deaths) as total_deaths, SUM(new_deaths)/SUM(new_Cases)*100 as DeathPercentage
From test.coviddeaths_csv
-- Where location_l = 'India'
where continent is not null 
-- Group By date_d
order by 1,2

-- Table test query
select *
from test.covidvaccinations_csv
where continent is not null
order by 3,4

-- Join tables for both table
-- Total population vc vaccination
select *
from test.coviddeaths_csv cc 
join test.covidvaccinations_csv cc2 
  on cc.location_l = cc2.location_l 
  and cc.date_d = cc2.date_d 
  where cc.continent is not null 
  order by 2,3

select cc.continent, cc.location_l, cc.date_d, cc.population, cc2.new_vaccinations, cc2.people_vaccinated as PeopleVaccinated
from test.coviddeaths_csv cc 
join test.covidvaccinations_csv cc2 
  on cc.location_l = cc2.location_l 
  and cc.date_d = cc2.date_d 
where cc.continent is not null  
order by 2,3

-- Population vaccinated percentage
select continent, location_l, date_d, population, new_vaccinations, people_vaccinated, (PeopleVaccinated/population)*100 as VaccinatedPopulationPercentage 
from (
select cc.continent, cc.location_l, cc.date_d, cc.population, cc2.new_vaccinations, cc2.people_vaccinated , cc2.people_vaccinated as PeopleVaccinated
from test.coviddeaths_csv cc 
join test.covidvaccinations_csv cc2 
  on cc.location_l = cc2.location_l 
  and cc.date_d = cc2.date_d 
where cc.continent is not null) as vpp
order by 2,3

-- table 
create temporary table if not exists percentpopulationvaccinated
(
continent varchar(255) NULL,
location_l varchar(255) NULL,
date_d date NULL,
population numeric NULL,
new_vaccinations numeric NULL,
people_vaccinated numeric NULL,
VaccinatedPopulationPercentage real NULL
) 

insert into percentpopulationvaccinated
select continent, location_l, date_d, population, new_vaccinations, people_vaccinated, (PeopleVaccinated/population)*100 as VaccinatedPopulationPercentage 
from (
select cc.continent, cc.location_l, cc.date_d, cc.population, cc2.new_vaccinations, cc2.people_vaccinated , cc2.people_vaccinated as PeopleVaccinated
from test.coviddeaths_csv cc 
join test.covidvaccinations_csv cc2 
  on cc.location_l = cc2.location_l 
  and cc.date_d = cc2.date_d 
where cc.continent is not null) as vpp
-- order by 2,3

select*
from percentpopulationvaccinated
order by 2,3

-- creating view for data visulal
create view percentpopulationvaccinated
as
select continent, location_l, date_d, population, new_vaccinations, people_vaccinated, (PeopleVaccinated/population)*100 as VaccinatedPopulationPercentage 
from (
select cc.continent, cc.location_l, cc.date_d, cc.population, cc2.new_vaccinations, cc2.people_vaccinated , cc2.people_vaccinated as PeopleVaccinated
from test.coviddeaths_csv cc 
join test.covidvaccinations_csv cc2 
  on cc.location_l = cc2.location_l 
  and cc.date_d = cc2.date_d 
where cc.continent is not null) as vpp
-- order by 2,3
