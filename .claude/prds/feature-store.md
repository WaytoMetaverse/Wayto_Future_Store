---
name: feature-store
description: Complete interior design platform with floor plan editor, material selection, and Unreal Engine rendering
status: backlog
created: 2025-09-21T12:55:54Z
---

# PRD: feature-store

## Executive Summary

The feature-store is a complete interior design platform that enables users to create floor plans, select materials and furniture, and generate high-quality renders via Unreal Engine. The system consists of a web-based 2D editor and a pure C++ Unreal Engine rendering backend.

## Problem Statement

### What problem are we solving?

Users need a complete workflow to:
1. **[P1]** Create and edit floor plans in a web browser
2. **[P1]** Select wall materials and apply them to specific walls
3. **[P1]** Place furniture objects in the space
4. **[P1]** Generate photorealistic renders via Unreal Engine
5. **[P2]** Browse and purchase real products through integrated catalog

### Why is this important now?

The floor plan editor is the foundation - without space creation, there are no walls to apply materials to and no spaces to place furniture. This validates the complete pipeline: Web Editor → JSON Layout → Unreal C++ → Rendered Output.

## User Stories

### Priority 1: Complete Core Workflow

**Floor Plan Creation:**
- As a user, I want to draw walls by clicking and dragging to create room layouts
- As a user, I want to add doors and windows to wall segments
- As a user, I want to edit wall thickness, height, and dimensions
- As a user, I want to see real-time validation of geometry (no self-intersections)

**Material Selection:**
- As a user, I want to select different materials for each wall surface
- As a developer, I want to add new materials to a data table that auto-syncs to web
- As a user, I want to see material options organized by type (paint, wallpaper, tile, etc.)

**Furniture Placement:**
- As a user, I want to drag furniture from a palette onto the floor plan
- As a user, I want to rotate furniture in 15° increments
- As a user, I want furniture to snap to walls automatically
- As a user, I want size validation (furniture fits in space)

**Rendering Pipeline:**
- As a user, I want to place camera points for panoramic and still renders
- As a user, I want to send my complete design to Unreal Engine
- As a user, I want to see render progress status
- As a user, I want to view completed 360° and still images

### Priority 2: Enhanced Features
- Product catalog with real furniture and materials
- One-click ordering for visualized items
- Advanced lighting and camera controls
- Multi-room projects and project management

## Requirements

### Priority 1: Core System Architecture

**Frontend 2D Editor:**
- Canvas-based wall drawing with point-to-point construction
- Real-time geometric validation (self-intersection, overlap detection)
- Door/window placement with centerParam+width+height definition
- Object palette with drag-drop placement and rotation
- Material selector interface integrated with data table
- Camera placement tools (panoramic and perspective views)
- JSON export following specified schema (all measurements in cm)

**Backend API & Data Management:**
- Materials data table with auto-sync mechanism
- Layout storage and versioning
- Render job queue management (SQS or equivalent)
- Asset management for textures and 3D models
- Real-time status updates for rendering progress

**Unreal Engine C++ Backend:**
- **Pure C++ implementation** (no Blueprints for core functionality)
- JSON layout parsing and validation
- Procedural wall geometry generation with extrusion
- Door/window opening creation via Boolean operations
- Floor and ceiling generation from wall outlines
- Material application system using Material Instances
- Furniture asset placement and material variant application
- Camera system supporting both panoramic and perspective rendering
- Movie Render Pipeline integration for high-quality output
- Automated file output to specified locations

### Technical Specifications

**Layout JSON Schema (cm units):**
```json
{
  "meta": {"projectId": "p_001", "version": 3, "unit": "cm"},
  "walls": [
    {"id": "w1", "start": [0,0], "end": [500,0], "thickness": 20, "height": 280, "materialId": "mat_paint_white"}
  ],
  "openings": [
    {"id": "d1", "wallId": "w1", "type": "door", "centerParam": 0.5, "width": 90, "height": 210, "sill": 0}
  ],
  "objects": [
    {"id": "o1", "category": "chair", "pos": [200,150], "rotDeg": 90, "catalogId": "chair.brandX.modelY"}
  ],
  "cameras": [
    {"id": "cam1", "type": "panorama", "pos": [250,200], "eyeHeight": 150}
  ],
  "materials": [
    {"wallId": "w1", "materialId": "mat_paint_blue", "surface": "interior"}
  ]
}
```

**Unreal C++ Architecture:**
- `ALayoutProcessor` - Main class for parsing JSON and coordinating generation
- `UWallGenerator` - Procedural wall mesh generation with openings
- `UMaterialManager` - Dynamic material instance creation and application
- `UFurnitureSpawner` - Asset loading and placement system
- `ACameraController` - Panoramic and perspective camera management
- `URenderJobExecutor` - Movie Render Pipeline automation
- `UFileOutputManager` - Automated image export and upload

### Non-Functional Requirements

**Performance:**
- 2D editor responsive at 60fps for basic operations
- Wall drawing with real-time validation under 16ms per frame
- Material selection updates immediately
- Unreal render job completion within 3 minutes (preview) / 10 minutes (final)

**Reliability:**
- Geometric validation prevents invalid layouts from reaching Unreal
- Graceful error handling for malformed JSON
- Render job retry mechanism for failed attempts
- Automatic cleanup of temporary files and assets

## Success Criteria

### Priority 1 Technical Validation
- ✅ Draw complete room layout with walls, doors, windows
- ✅ Apply different materials to different walls
- ✅ Place and arrange furniture with proper validation
- ✅ Generate JSON layout conforming to schema
- ✅ Unreal C++ processes JSON and generates geometry
- ✅ Materials applied correctly to wall surfaces
- ✅ Furniture placed with accurate positioning
- ✅ Complete panoramic and still renders generated
- ✅ Images returned to web interface for display

### Performance Targets
- End-to-end workflow (design → render → display) under 15 minutes
- 2D editor handles rooms up to 20 walls without performance issues
- Unreal system processes layouts with 50+ furniture items
- 95% successful render completion rate

## Implementation Roadmap

### Phase 1A: 2D Editor Foundation (Week 1-2)
- Canvas-based wall drawing system
- Point-to-point wall construction with thickness/height
- Real-time geometric validation
- Door/window placement interface
- JSON export functionality

### Phase 1B: Material & Object Systems (Week 2-3)
- Material data table and sync system
- Material selection UI integrated with walls
- Furniture palette and drag-drop placement
- Rotation and wall-snapping functionality
- Object size validation

### Phase 1C: Camera & Render Interface (Week 3-4)
- Camera placement tools (panoramic/perspective)
- Render job submission interface
- Status tracking and progress display
- Result image display system

### Phase 2: Unreal C++ Backend (Week 4-7)
- Core C++ classes for layout processing
- Procedural wall generation with openings
- Material system with dynamic instance creation
- Furniture placement and asset management
- Camera system and render pipeline integration
- Automated output and file management

### Phase 3: Integration & Testing (Week 7-8)
- End-to-end pipeline testing
- Error handling and edge case resolution
- Performance optimization
- Quality assurance and user testing

## Technical Dependencies

### Frontend Requirements
- Modern web framework (React + TypeScript recommended)
- Canvas/WebGL library for 2D drawing (Konva.js or similar)
- Real-time validation engine for geometry
- Material preview system
- File upload/download capabilities

### Backend Requirements
- RESTful API framework (Node.js/NestJS recommended)
- Database for materials and layout storage
- Message queue system (SQS or Redis)
- File storage and CDN (S3 + CloudFront)

### Unreal Engine Requirements
- **Unreal Engine 5.6+ with pure C++ project setup**
- Movie Render Pipeline plugin
- Command-line automation capabilities
- High-quality material assets and furniture models
- Windows development environment (initial target)

## Constraints & Assumptions

### Technical Constraints
- All measurements in centimeters (no unit conversion)
- Right-handed coordinate system (X→right, Y→up)
- Windows-only Unreal pipeline initially
- Pure C++ implementation for Unreal backend

### Content Assumptions
- Basic material library will be manually created initially
- Furniture assets provided in compatible formats (FBX/GLB)
- Standard PBR material workflow
- Preset lighting configurations for consistent results

## Risk Assessment

### Critical Technical Risks
1. **C++ Complexity:** Pure C++ Unreal implementation may be challenging
2. **Performance:** Real-time 2D editor validation with complex geometry
3. **Integration:** JSON schema consistency between web and Unreal
4. **Asset Management:** Large texture/model files affecting performance

### Mitigation Strategies
1. Start with simple C++ classes, gradually add complexity
2. Implement geometric validation in Web Workers to avoid UI blocking
3. Comprehensive JSON schema validation on both ends
4. Asset optimization pipeline with LOD generation

## Success Measurement

### Development Milestones
- [ ] 2D editor can create valid room layouts
- [ ] Material selection system functional
- [ ] Furniture placement with validation working
- [ ] JSON export matches required schema
- [ ] Unreal C++ successfully processes JSON
- [ ] End-to-end render pipeline operational
- [ ] Quality renders generated and displayed

### Quality Gates
- Geometric validation catches 100% of invalid layouts
- Material application accuracy 100% (correct materials on correct walls)
- Furniture placement accuracy within 1cm tolerance
- Render output quality meets photorealistic standards

**Critical Note:** The floor plan editor is foundational - all subsequent features depend on this core functionality working reliably.