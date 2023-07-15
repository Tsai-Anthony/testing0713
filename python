import discord
import asyncio
import requests
from bs4 import BeautifulSoup

TOKEN = 'YOUR_DISCORD_BOT_TOKEN'
WEBSITE_URL = 'https://www.indeed.com/jobs?q=test+automation'  # Indeed URL for test automation jobs

client = discord.Client()
last_job_link = None

@client.event
async def on_ready():
    print(f'Logged in as {client.user.name} ({client.user.id})')
    print('------')

@client.event
async def on_message(message):
    # Ignore messages sent by the bot itself
    if message.author == client.user:
        return

@client.event
async def on_ready():
    print(f'Logged in as {client.user.name} ({client.user.id})')
    print('------')

    await send_job_notifications()

async def send_job_notifications():
    while True:
        try:
            # Send a GET request to the website and retrieve the page content
            response = requests.get(WEBSITE_URL)
            page_content = response.content

            # Parse the HTML using BeautifulSoup
            soup = BeautifulSoup(page_content, 'html.parser')

            # Find the job listings on the page and extract relevant information
            job_listings = soup.find_all('div', class_='jobsearch-SerpJobCard')
            latest_job_link = None

            for job_listing in job_listings:
                # Extract job information (title, company, link, etc.)
                job_title = job_listing.find('h2', class_='title').text.strip()
                job_company = job_listing.find('span', class_='company').text.strip()
                job_link = 'https://www.indeed.com' + job_listing.find('a')['href']

                if last_job_link is None or job_link != last_job_link:
                    # Customize the message content and format according to your needs
                    notification_message = f'New test automation job posted:\nTitle: {job_title}\nCompany: {job_company}\nLink: {job_link}'

                    for guild in client.guilds:
                        for channel in guild.text_channels:
                            await channel.send(notification_message)

                    if latest_job_link is None:
                        latest_job_link = job_link

            last_job_link = latest_job_link

        except requests.exceptions.RequestException as e:
            print(f'Error occurred while fetching job data: {e}')

        await asyncio.sleep(3600)  # Sleep for 1 hour before checking for new jobs again

client.run(TOKEN)
