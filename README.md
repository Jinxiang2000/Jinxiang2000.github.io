# Jinxiang Ma - Portfolio Website

A modern, responsive portfolio website built with Hugo and the Blowfish theme, showcasing my data science projects, skills, and professional experience.

## 🌟 Features

- **Responsive Design**: Beautiful, mobile-first design that works on all devices
- **Interactive Skill Tree**: Dynamic visualization of technical competencies
- **Project Showcase**: Detailed project pages with technical implementations
- **Blog Integration**: Platform for sharing insights and technical articles
- **Professional Profile**: Comprehensive about section and resume integration
- **Modern UI**: Clean, professional design with dark/light theme support

## 🛠️ Built With

- **[Hugo](https://gohugo.io/)**: Fast and flexible static site generator
- **[Blowfish Theme](https://blowfish.page/)**: Beautiful and feature-rich Hugo theme
- **[D3.js](https://d3js.org/)**: Interactive data visualizations
- **GitHub Pages**: Automated deployment and hosting
- **GitHub Actions**: CI/CD pipeline for seamless updates

## 🚀 Quick Start

### Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) (v0.147.8 or later)
- [Git](https://git-scm.com/)

### Local Development

1. **Clone the repository**
   ```bash
   git clone https://github.com/Jinxiang2000/portfolio.git
   cd portfolio
   ```

2. **Initialize submodules**
   ```bash
   git submodule update --init --recursive
   ```

3. **Start the development server**
   ```bash
   hugo server --buildDrafts
   ```

4. **Open in browser**
   Visit `http://localhost:1313` to view the site

### Building for Production

```bash
hugo --minify
```

The built site will be available in the `public/` directory.

## 📁 Project Structure

```
portfolio/
├── content/
│   ├── about/              # About page
│   ├── projects/           # Project showcase pages
│   ├── posts/              # Blog posts
│   ├── skills/             # Skills and expertise
│   └── _index.md           # Homepage content
├── static/
│   ├── img/                # Images and assets
│   └── flare.json          # Skill tree data
├── config/
│   └── _default/           # Hugo configuration files
├── .github/
│   └── workflows/          # GitHub Actions CI/CD
└── themes/
    └── blowfish/           # Theme submodule
```

## 🎨 Customization

### Adding New Projects

1. Create a new markdown file in `content/projects/`
2. Add project metadata in the frontmatter
3. Include project images in `static/img/`
4. Update the projects index page if needed

### Modifying the Skill Tree

1. Edit `static/flare.json` to update skills and categories
2. The interactive visualization will automatically reflect changes

### Updating Personal Information

1. Modify `config/_default/languages.en.toml` for profile information
2. Update `content/about/_index.md` for detailed biography
3. Replace images in `static/img/` with your own

## 🔧 Configuration

Key configuration files:

- `config/_default/config.toml`: Main site configuration
- `config/_default/params.toml`: Theme-specific parameters
- `config/_default/menus.en.toml`: Navigation menu structure
- `config/_default/languages.en.toml`: Language and author settings

## 📱 Responsive Design

The site is optimized for all screen sizes:

- **Desktop**: Full-featured layout with sidebar navigation
- **Tablet**: Responsive grid layout with collapsible navigation
- **Mobile**: Mobile-first design with touch-friendly interactions

## 🔍 SEO Optimized

- Semantic HTML structure
- Open Graph meta tags
- Twitter Card support
- XML sitemap generation
- Schema.org structured data

## 📊 Analytics Ready

Easy integration with:

- Google Analytics
- Fathom Analytics
- Other privacy-focused analytics platforms

## 🚀 Deployment

The site is automatically deployed to GitHub Pages via GitHub Actions. Every push to the `main` branch triggers a new deployment.

### Manual Deployment

For other hosting platforms:

1. Build the site: `hugo --minify`
2. Upload the `public/` directory to your web server

## 🤝 Contributing

While this is a personal portfolio, suggestions and improvements are welcome:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

## 📞 Contact

- **Email**: majinxiang2000@gmail.com
- **LinkedIn**: [jinxiang-ma-806a621a8](https://www.linkedin.com/in/jinxiang-ma-806a621a8/)
- **GitHub**: [Jinxiang2000](https://github.com/Jinxiang2000)
- **Kaggle**: [jinxiangma](https://www.kaggle.com/jinxiangma)

---

⭐ **Star this repo if you found it helpful!** 