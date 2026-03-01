# ywt0209.github.io

Personal academic website of **Yu-Wen Tseng (曾昱文)**.

🔗 **Live site:** [https://ywt0209.github.io](https://ywt0209.github.io)

## About

Ph.D. student in Computer Science & Information Engineering at National Taiwan University (NTU), working on vision-centric perception for autonomous driving — semantic occupancy prediction and test-time adaptation.

## Structure

```
├── index.html          # English homepage
├── zh.html             # Chinese (繁體中文) homepage
├── assets/
│   ├── css/style.css   # Stylesheet
│   └── images/         # Profile photo and other images
├── resume/
│   ├── resume.tex      # LaTeX source
│   ├── resume-style.sty
│   └── resume.pdf      # Compiled PDF
└── .gitignore
```

## Development

Static HTML/CSS — no build tools required. To preview locally:

```bash
python3 -m http.server 8000
```

Then open [http://localhost:8000](http://localhost:8000).

## Resume

The resume is written in LaTeX. To recompile:

```bash
cd resume
pdflatex resume.tex
```

## License

Content © 2026 Yu-Wen Tseng. All rights reserved.
