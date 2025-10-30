# Connexins - Guestbook Application

A modern, serverless guestbook application built with Next.js and FaunaDB. This project demonstrates how to build a full-stack application with GraphQL, serverless functions, and static site generation.

## Features

- 📝 **Create Guestbook Entries** - Visitors can sign the guestbook with their name and message
- 🔄 **Real-time Updates** - Uses SWR for optimistic UI updates and automatic revalidation
- 🎨 **Dark Mode Support** - Beautiful UI with Tailwind CSS and dark mode
- ⚡ **Serverless Architecture** - Powered by Next.js API routes and FaunaDB
- 🔒 **Secure** - Role-based access control with FaunaDB
- 📱 **Responsive Design** - Works seamlessly on all devices
- 🚀 **Static Site Generation** - Pre-renders pages at build time for optimal performance

## Tech Stack

- **Framework**: [Next.js](https://nextjs.org/) (latest)
- **Database**: [FaunaDB](https://fauna.com/) with GraphQL
- **Styling**: [Tailwind CSS](https://tailwindcss.com/) v3
- **State Management**: [SWR](https://swr.vercel.app/) for data fetching
- **Date Formatting**: [date-fns](https://date-fns.org/)
- **Language**: JavaScript (React)

## Prerequisites

Before you begin, ensure you have the following installed:
- Node.js (v14 or higher)
- Yarn or npm
- A [FaunaDB](https://fauna.com/) account

## Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd connexins
```

### 2. Install Dependencies

```bash
yarn install
# or
npm install
```

### 3. Set Up FaunaDB

1. Create a new database in your [FaunaDB dashboard](https://dashboard.fauna.com/)
2. Generate an admin key for your database:
   - Go to Security → Keys
   - Create a new key with the "Admin" role
   - Save the secret key

### 4. Configure Environment Variables

Run the setup script to configure your database and create the necessary resources:

```bash
yarn setup
# or
npm run setup
```

The script will:
- Prompt you for your FaunaDB admin key (or use `FAUNA_ADMIN_KEY` environment variable)
- Import the GraphQL schema
- Create the necessary indexes and functions
- Set up role-based access control
- Generate a `.env.local` file with your client secret

Alternatively, you can manually create a `.env.local` file:

```bash
FAUNA_CLIENT_SECRET=your_fauna_client_secret_here
```

### 5. Run the Development Server

```bash
yarn dev
# or
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser to see the application.

## Project Structure

```
connexins/
├── components/           # React components
│   ├── ErrorMessage.js   # Error display component
│   ├── LoadingSpinner.js # Loading indicator
│   └── SuccessMessage.js # Success notification
├── lib/                  # Utility libraries
│   ├── constants.js      # Configuration constants
│   └── fauna.js          # FaunaDB GraphQL client and queries
├── pages/                # Next.js pages
│   ├── index.js          # Main guestbook page
│   └── api/              # API routes
│       └── entries/      # Guestbook entries endpoints
│           └── index.js  # GET and POST handlers
├── public/               # Static assets
│   └── static/           # Static files (favicon, etc.)
├── scripts/              # Setup and utility scripts
│   └── setup.js          # Database setup script
├── schema.gql            # FaunaDB GraphQL schema
├── tailwind.config.js    # Tailwind CSS configuration
├── postcss.config.js     # PostCSS configuration
└── package.json          # Project dependencies
```

## Database Schema

The application uses a simple GraphQL schema defined in `schema.gql`:

```graphql
type GuestbookEntry {
  name: String!
  message: String!
  createdAt: Time!
}

type Query {
  entries: [GuestbookEntry!]
    @resolver(name: "listLatestEntries", paginated: true)
}
```

The setup script creates:
- **Collection**: `GuestbookEntry` - Stores all guestbook entries
- **Index**: `latestEntries` - Indexes entries by creation date (descending)
- **Function**: `listLatestEntries` - Custom resolver for paginated queries
- **Role**: `GuestbookRole` - Defines read/write permissions
- **Key**: Client secret with limited permissions

## API Endpoints

### GET `/api/entries`
Retrieves all guestbook entries, sorted by creation date (newest first).

**Response:**
```json
[
  {
    "_id": "123456789",
    "_ts": 1234567890,
    "name": "John Doe",
    "message": "Great guestbook!",
    "createdAt": "2025-10-29T12:00:00Z"
  }
]
```

### POST `/api/entries`
Creates a new guestbook entry.

**Request Body:**
```json
{
  "name": "John Doe",
  "message": "Hello, world!"
}
```

**Response:**
```json
{
  "_id": "123456789",
  "_ts": 1234567890,
  "name": "John Doe",
  "message": "Hello, world!",
  "createdAt": "2025-10-29T12:00:00Z"
}
```

## Build and Deployment

### Build for Production

```bash
yarn build
# or
npm run build
```

This command will:
1. Run the setup script (if not already configured)
2. Build the Next.js application
3. Generate static pages

### Start Production Server

```bash
yarn start
# or
npm start
```

### Deploy to Vercel

This project is optimized for deployment on [Vercel](https://vercel.com):

1. Push your code to GitHub
2. Import your repository on Vercel
3. Add the `FAUNA_ADMIN_KEY` environment variable in your Vercel project settings
4. Deploy!

The setup script will automatically run during the build process on Vercel.

## Development

### Key Features Implementation

- **Optimistic Updates**: The application uses SWR's `mutate` function to immediately update the UI after submitting an entry
- **Error Handling**: Comprehensive error states with user-friendly messages
- **Loading States**: Visual feedback during data fetching and form submission
- **Static Generation**: Uses `getStaticProps` to pre-render the page with initial data
- **Form Validation**: Required fields with HTML5 validation

### Customization

- **Styling**: Modify `tailwind.config.js` to customize the theme
- **Page Limit**: Change the `size` parameter in `lib/fauna.js` to adjust the number of entries displayed
- **Date Format**: Update the format string in `pages/index.js` to change date display

## Troubleshooting

### "Invalid database secret" error
- Ensure your FaunaDB admin key is correct
- Check that you're using the right key for the correct database

### Setup script fails
- Make sure you have the necessary permissions in FaunaDB
- Verify that the schema file (`schema.gql`) exists and is valid
- Try creating a fresh database and running the setup again

### API routes return 500 errors
- Check that your `.env.local` file exists and contains the correct `FAUNA_CLIENT_SECRET`
- Verify that the database resources were created successfully
- Review the FaunaDB dashboard for any error logs

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
