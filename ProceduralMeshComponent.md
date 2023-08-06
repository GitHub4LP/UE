# ProceduralMeshComponent
## PrivateDependencyModuleNames
```c#
"RenderCore","ShaderCore","RHI",
```
## MyPComp.h
```c++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "Engine.h"
#include "MyPComp.generated.h"

UCLASS(meta = (BlueprintSpawnableComponent))
class TRYPLUGIN_API UMyPComp : public UPrimitiveComponent
{
	GENERATED_BODY()

public:
	virtual FPrimitiveSceneProxy* CreateSceneProxy()override;
};
```
## MyPComp.cpp
```c++
// Fill out your copyright notice in the Description page of Project Settings.

#include "MyPComp.h"
#include "DynamicMeshBuilder.h"


class FMyPCompSceneProxy final
	:public FPrimitiveSceneProxy
{
private:
	FLocalVertexFactory VertexFactory;
	FDynamicMeshIndexBuffer32 IndexBuffer;
	FStaticMeshVertexBuffers VertexBuffers;

public:
	FMyPCompSceneProxy(const UPrimitiveComponent* InComponent)
		:FPrimitiveSceneProxy(InComponent),
		VertexFactory(GetScene().GetFeatureLevel(), "FMyPCompSceneProxy")
	{
		float BoxSize = 100.f;

		const FColor VertexColor(255, 255, 255);

		TArray<FDynamicMeshVertex> Vertices;

		Vertices.AddUninitialized(4);
		IndexBuffer.Indices.AddUninitialized(4 * 3);

		TArray<FVector> Vectors;

		Vectors.Add(FVector(0.f));
		Vectors.Add(FVector(BoxSize, 0.f, 0.f));
		Vectors.Add(FVector(0.f, BoxSize, 0.f));
		Vectors.Add(FVector(0.f, 0.f, BoxSize));

		FDynamicMeshVertex Vertex;
		Vertex.Color = VertexColor;
		Vertex.TangentX = FVector(1, 0, 0);
		Vertex.TangentZ = FVector(0, 0, 1);
		Vertex.TangentZ.Vector.W = 127;
		Vertex.TextureCoordinate[0].X = 0.0f;
		Vertex.TextureCoordinate[0].Y = 0.0f;

		for (int32 i = 0; i < Vectors.Num(); i++)
		{
			Vertex.Position = Vectors[i];
			Vertices[i] = Vertex;
		}

		IndexBuffer.Indices.Add(0);
		IndexBuffer.Indices.Add(1);
		IndexBuffer.Indices.Add(2);
		IndexBuffer.Indices.Add(0);
		IndexBuffer.Indices.Add(2);
		IndexBuffer.Indices.Add(3);
		IndexBuffer.Indices.Add(0);
		IndexBuffer.Indices.Add(3);
		IndexBuffer.Indices.Add(1);
		IndexBuffer.Indices.Add(3);
		IndexBuffer.Indices.Add(2);
		IndexBuffer.Indices.Add(1);

		VertexBuffers.InitFromDynamicVertex(&VertexFactory, Vertices);
		BeginInitResource(&VertexBuffers.PositionVertexBuffer);
		BeginInitResource(&VertexBuffers.StaticMeshVertexBuffer);
		BeginInitResource(&VertexBuffers.ColorVertexBuffer);
		BeginInitResource(&IndexBuffer);
		BeginInitResource(&VertexFactory);
	}

	~FMyPCompSceneProxy()
	{
		VertexBuffers.PositionVertexBuffer.ReleaseResource();
		VertexBuffers.StaticMeshVertexBuffer.ReleaseResource();
		VertexBuffers.ColorVertexBuffer.ReleaseResource();
		IndexBuffer.ReleaseResource();
		VertexFactory.ReleaseResource();
	}

	virtual uint32 GetMemoryFootprint(void)const override
	{
		return (sizeof(*this) + GetAllocatedSize());
	}
	virtual FPrimitiveViewRelevance GetViewRelevance(const FSceneView* View)const override
	{
		FPrimitiveViewRelevance Result;
		Result.bStaticRelevance = true;
		Result.bDrawRelevance = IsShown(View);
		Result.bDynamicRelevance = true;
		Result.bShadowRelevance = IsShown(View);
		Result.bEditorPrimitiveRelevance = UseEditorCompositing(View);
		return Result;
	}
	virtual SIZE_T GetTypeHash() const override
	{
		static SIZE_T UniquePointer;
		return reinterpret_cast<SIZE_T>(&UniquePointer);
	}
	void DrawMyMesh(FMeshBatch& Mesh)
	{
		FMeshBatchElement& BatchElement = Mesh.Elements[0];
		BatchElement.IndexBuffer = &IndexBuffer;
		Mesh.bWireframe = false;
		Mesh.VertexFactory = &VertexFactory;
		Mesh.MaterialRenderProxy = UMaterial::GetDefaultMaterial(MD_Surface)->GetRenderProxy(false);
		BatchElement.PrimitiveUniformBuffer = CreatePrimitiveUniformBufferImmediate(GetLocalToWorld(), GetBounds(), GetLocalBounds(), true, UseEditorDepthTest());
		BatchElement.FirstIndex = 0;
		BatchElement.NumPrimitives = IndexBuffer.Indices.Num() / 3;
		BatchElement.MinVertexIndex = 0;
		BatchElement.MaxVertexIndex = VertexBuffers.PositionVertexBuffer.GetNumVertices() - 1;
		Mesh.ReverseCulling = IsLocalToWorldDeterminantNegative();
		Mesh.Type = PT_TriangleList;
		Mesh.DepthPriorityGroup = SDPG_World;
		Mesh.bCanApplyViewModeOverrides = false;
	}
	virtual void DrawStaticElements(FStaticPrimitiveDrawInterface* PDI)override
	{
		FMeshBatch Mesh;
		DrawMyMesh(Mesh);
		PDI->DrawMesh(Mesh, 1.f);
	}
	virtual void GetDynamicMeshElements(const TArray<const FSceneView *>& Views, const FSceneViewFamily& ViewFamily, uint32 VisibilityMap, class FMeshElementCollector& Collector)const override
	{
		FBox TestDynamicBox = FBox(FVector(-100.f), FVector(100.f));
		DrawWireBox(Collector.GetPDI(0), GetLocalToWorld(), TestDynamicBox, FLinearColor::Red, ESceneDepthPriorityGroup::SDPG_Foreground, 10.f);

#if 0
		for (int32 ViewIndex = 0; ViewIndex < Views.Num(); ViewIndex++)
		{
			if (VisibilityMap & (1 << ViewIndex))
			{
				const FSceneView* View = Views[ViewIndex];
				// Draw the mesh.
				FMeshBatch& Mesh = Collector.AllocateMesh();
				DrawMyMesh(Mesh);
				Collector.AddMesh(ViewIndex, Mesh);
			}
		}
#endif
	}
};


FPrimitiveSceneProxy* UMyPComp::CreateSceneProxy()
{
	return new FMyPCompSceneProxy(this);
}
```