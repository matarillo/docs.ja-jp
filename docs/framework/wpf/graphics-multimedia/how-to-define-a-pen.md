---
title: '方法: ペンを定義する'
ms.date: 03/30/2017
dev_langs:
- csharp
- vb
helpviewer_keywords:
- pens [WPF], defining
- creating [WPF], pens
ms.assetid: 7a4f2900-cdf9-49de-84e0-ba5d0ded4d33
ms.openlocfilehash: 1d69a40604dbf2f7a73c17279ae946faeb6c634a
ms.sourcegitcommit: 9b552addadfb57fab0b9e7852ed4f1f1b8a42f8e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "61965400"
---
# <a name="how-to-define-a-pen"></a>方法: ペンを定義する
この例では、<xref:System.Windows.Media.Pen>図形のアウトラインにします。 単純なを作成する<xref:System.Windows.Media.Pen>、だけを指定する必要があるその<xref:System.Windows.Media.Pen.Thickness%2A>と<xref:System.Windows.Media.Pen.Brush%2A>します。 複雑なペンを作成するには指定することによって、 <xref:System.Windows.Media.Pen.DashStyle%2A>、 <xref:System.Windows.Media.Pen.DashCap%2A>、 <xref:System.Windows.Media.Pen.LineJoin%2A>、 <xref:System.Windows.Media.Pen.StartLineCap%2A>、および<xref:System.Windows.Media.Pen.EndLineCap%2A>します。  
  
## <a name="example"></a>例  
 次の例では、<xref:System.Windows.Media.Pen>で定義された図形のアウトラインを<xref:System.Windows.Media.GeometryDrawing>します。  
  
 [!code-csharp[PenExamples_snip#PenExampleWholePage](~/samples/snippets/csharp/VS_Snippets_Wpf/PenExamples_snip/CSharp/PenExample.cs#penexamplewholepage)]
 [!code-vb[PenExamples_snip#PenExampleWholePage](~/samples/snippets/visualbasic/VS_Snippets_Wpf/PenExamples_snip/VisualBasic/PenExample.vb#penexamplewholepage)]  
  
 ![ペンで描いたアウトライン](./media/graphicsmm-simple-pen.jpg "graphicsmm_simple_pen")  
GeometryDrawing
