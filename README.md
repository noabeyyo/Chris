  let letters = [];
    let gravity = 0.05; // Reduced gravity for gentle falling
    const numLetters = 10;

    function setup() {
      createCanvas(400, 400);
      for (let i = 0; i < numLetters; i++) {
        const x = width * 0.1 + i * 30;
        const y = random(-height, -10); // Random initial y position with a wider range
        const rotationSpeed = random(-0.2, 0.2); // Random rotation speed with a wider range
        letters.push(new Letter(x, y, rotationSpeed));
      }
    }

    function draw() {
      background(220);

      for (let letter of letters) {
        letter.applyGravity(gravity);
        letter.floatWithHover();
        letter.display();
      }
    }

    class Letter {
      constructor(x, y, rotationSpeed) {
        this.x = x;
        this.y = y;
        this.speedY = 0;
        this.rotationSpeed = rotationSpeed;
        this.letter = random('ABCDEFGHIJKLMNOPQRSTUVWXYZ');
      }

      applyGravity(gravity) {
        this.speedY += gravity;
        this.y += this.speedY;

        // Pile up the letters at the bottom
        if (this.y > height) {
          this.y = height;
          this.speedY *= -0.6; // Bounce back up with reduced velocitya
        }

        // Check for collisions with other letters and adjust the y-coordinate
        for (let other of letters) {
          if (other !== this) {
            const overlap = 1; // Minimum distance between letters
            const minDistance = textWidth(this.letter) / 2 + textWidth(other.letter) / 2 + overlap;
            const distance = dist(this.x, this.y, other.x, other.y);
            if (distance < minDistance) {
              const pushForce = (minDistance - distance) * 0.5;
              if (this.y < other.y) {
                this.y -= pushForce;
                this.speedY = 0;
              } else {
                this.y += pushForce;
              }
            }
          }
        }

        // Constrain the position to stay within the canvas borders
        this.x = constrain(this.x, 0, width);
        this.y = constrain(this.y, 0, height);
      }

      floatWithHover() {
        const hoverDist = 20;
        if (dist(mouseX, mouseY, this.x, this.y) < hoverDist) {
          // Move based on the difference between mouse position and letter's position
          const moveX = mouseX - pmouseX;
          const moveY = mouseY - pmouseY;
          this.x += moveX;
          this.y += moveY;
        }
      }

      display() {
        textAlign(CENTER);
        textSize(20);
        fill(0);
        push(); // Save the current transformation matrix
        translate(this.x, this.y); // Move the origin to the letter's position
        rotate(this.rotationSpeed); // Rotate the letter based on rotationSpeed
        text(this.letter, 0, 0); // Draw the letter at the origin
        pop(); // Restore the previous transformation matrix
      }
    }
