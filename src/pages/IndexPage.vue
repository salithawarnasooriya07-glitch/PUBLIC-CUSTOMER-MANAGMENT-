<template>
  <q-page class="flex flex-center bg-white relative-position overflow-hidden">
    <!-- Physics Canvas Container -->
    <div ref="physicsContainer" class="absolute-full physics-layer"></div>

    <!-- Hero Overlay -->
    <div class="relative-position z-top text-center q-pa-md content-layer">
      <div class="hero-content">
        <h1 class="text-h1 text-weight-bolder q-mb-none text-dark tracking-tight big-title">
          bossofict<span class="text-accent-green">.</span>
        </h1>
        <p class="text-h5 text-grey-8 q-mt-md q-mb-lg font-weight-light">
          Salitha Warnasooriya <span class="text-grey-5">|</span> ICT Academy
        </p>
        
        <div class="q-gutter-md row justify-center">
            <q-btn
            unelevated
            color="dark"
            text-color="white"
            label="Join Class"
            class="q-px-xl q-py-sm text-subtitle1 rounded-btn hover-lift"
            no-caps
            />
            <q-btn
            outline
            color="grey-9"
            label="View Courses"
            class="q-px-xl q-py-sm text-subtitle1 rounded-btn"
            no-caps
            />
        </div>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { onMounted, onBeforeUnmount, ref, nextTick } from 'vue'
import Matter from 'matter-js'

const physicsContainer = ref(null)
let engine = null
let render = null
let runner = null

const initPhysics = () => {
  if (!physicsContainer.value) return

  // Module aliases
  const Engine = Matter.Engine,
        Render = Matter.Render,
        Runner = Matter.Runner,
        Bodies = Matter.Bodies,
        Composite = Matter.Composite,
        Mouse = Matter.Mouse,
        MouseConstraint = Matter.MouseConstraint;

  // Create an engine
  engine = Engine.create();

  // Create a renderer
  render = Render.create({
    element: physicsContainer.value,
    engine: engine,
    options: {
      width: physicsContainer.value.clientWidth,
      height: physicsContainer.value.clientHeight,
      background: 'transparent',
      wireframes: false,
      pixelRatio: window.devicePixelRatio
    }
  });

  // Create bodies
  // We will create some "ICT" blocks: Square/Rectangular bodies
  const width = physicsContainer.value.clientWidth;
  const height = physicsContainer.value.clientHeight;

  const ground = Bodies.rectangle(width / 2, height + 30, width, 60, { 
    isStatic: true,
    render: { visible: false } 
  });
  
  const leftWall = Bodies.rectangle(-30, height / 2, 60, height, { 
    isStatic: true,
    render: { visible: false }
  });
  
  const rightWall = Bodies.rectangle(width + 30, height / 2, 60, height, { 
    isStatic: true,
    render: { visible: false }
  });

  // Abstract shapes representing "tech/ideas"
  const shapes = []
  const colors = ['#10B981', '#333333', '#e5e7eb', '#000000']
  
  for (let i = 0; i < 15; i++) {
    const x = Math.random() * width
    const y = Math.random() * -500 // Start above the screen
    const size = 40 + Math.random() * 40
    
    // Mix of Chamfered Rectangles (Chips) and Circles
    let body;
    if (Math.random() > 0.5) {
        body = Bodies.rectangle(x, y, size, size, {
            chamfer: { radius: 10 },
            render: {
                fillStyle: colors[Math.floor(Math.random() * colors.length)],
                strokeStyle: '#ffffff',
                lineWidth: 2
            }
        })
    } else {
         body = Bodies.circle(x, y, size / 2, {
            render: {
                fillStyle: colors[Math.floor(Math.random() * colors.length)],
                strokeStyle: '#ffffff',
                lineWidth: 2
            }
        })
    }
    
    body.restitution = 0.6; // Bouncy
    shapes.push(body)
  }

  Composite.add(engine.world, [ground, leftWall, rightWall, ...shapes]);

  // Add mouse control
  const mouse = Mouse.create(render.canvas);
  const mouseConstraint = MouseConstraint.create(engine, {
    mouse: mouse,
    constraint: {
      stiffness: 0.2,
      render: {
        visible: false
      }
    }
  });

  Composite.add(engine.world, mouseConstraint);

  // Keep the mouse in sync with rendering
  render.mouse = mouse;

  // Run the renderer
  Render.run(render);

  // Create runner
  runner = Runner.create();
  Runner.run(runner, engine);
}

onMounted(async () => {
    // Wait for DOM to be ready
    await nextTick()
    // Small delay to ensure container sizing is correct
    setTimeout(initPhysics, 100)
    
    // Handle Window Resize
    window.addEventListener('resize', handleResize)
})

const handleResize = () => {
    if (!render || !physicsContainer.value) return;
    
    render.canvas.width = physicsContainer.value.clientWidth;
    render.canvas.height = physicsContainer.value.clientHeight;
    
    // We would ideally reposition walls here, but for a simple demo, a reload is safer or just letting them fall out
    // Re-initializing for simplicity in this specific "wow" context might be better, but let's just update bounds if possible.
    // For now, simpler is better: ignore complex resize mechanics to avoid bugs.
}

onBeforeUnmount(() => {
  window.removeEventListener('resize', handleResize)
  if (render) {
    Matter.Render.stop(render)
    render.canvas.remove()
  }
  if (runner) {
    Matter.Runner.stop(runner)
  }
})
</script>

<style scoped>
.physics-layer {
  z-index: 1;
}

.content-layer {
  z-index: 2;
  pointer-events: none; /* Let clicks pass through to physics layer unless on buttons */
}

.hero-content {
    pointer-events: auto; /* Enable clicks on the text/buttons */
    background: rgba(255, 255, 255, 0.85); /* Glassy backdrop for text legibility */
    padding: 2rem 4rem;
    border-radius: 24px;
    backdrop-filter: blur(10px);
    box-shadow: 0 20px 50px rgba(0,0,0,0.05);
    border: 1px solid rgba(255, 255, 255, 0.6);
}

.big-title {
    font-size: 5rem;
    line-height: 1.1;
}

@media (max-width: 600px) {
    .big-title {
        font-size: 3rem;
    }
    .hero-content {
        padding: 2rem 1rem;
    }
}

.text-accent-green {
  color: #10B981;
}

.hover-lift {
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}
.hover-lift:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(0,0,0,0.1);
}

.rounded-btn {
  border-radius: 12px;
}
</style>
